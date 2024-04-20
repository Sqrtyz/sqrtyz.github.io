+++
title = '【笔记】数据库系统 (Part II)'
date = 2024-01-01T07:07:07+01:00
+++

## Lecture 4. Advanced SQL

### SQL Data Types and Schemas

之前提到 SQL 的关系模式（表头）schemas $R=(A_1, A_2, \cdots, A_n)$。其中属性 $A_i$ 具有定义域 $D_i$。

+ 自定义 domain

    ```sql
    CREATE new domain 
    CREATE domain Dollars AS numeric(12, 2) NOT NULL; 
    CREATE domain Pounds AS numeric(12,2); 
    CREATE TABLE employee 
    (eno char(10) primary key, 
    ename varchar(15), 
    job varchar(10), 
    salary Dollars, 
    comm Pounds);
    ```
 
+ Object types as domain

    + **blob**: binary large object -- object is a large collection of uninterpreted binary data (whose interpretation is left to an application outside of the database system) 

    + **clob**: character large object -- object is a large collection of character data

    + Example

        ```sql
        CREATE TABLE students 
        (sid char(10) primary key, 
        name varchar(10), 
        gender char(1), 
        photo blob(20MB), 
        cv clob(10KB))
        ```

### Integrity Constraints

+ Domain Constraints

    对于单个 Relation 的常见约束：`Not null`, `Primary key`, `Unique`, `Check (P)` (where P is a predicate). 

+ Referential Integrity

    事实上，这种约束也可以「跨表」。回顾我们之前提及的 Foreign Key：

    其中 $r_1(R_1)$ 是被参照关系，$r_2(R_2)$ 是参照关系。

    ![dbs-18](images/dbs-18.png)

    + Checking Referential Integrity on Database Modification

        假设我们有 $r_2(R_2) \to r_1(R_1)$ 的参照与被参照关系，为了保证此关系的始终合法，在对 $r_1(R_1)$ 或 $r_2(R_2)$ 进行修改操作时，必须同时关心另一方是否需要变动以维持该关系。

        ![dbs-19](images/dbs-19.png)

    + Referential Integrity in SQL

        在 SQL 语言中，我们也可以描述这种参照与被参照关系。回顾之前的 Banking Example：

        ![dbs-3](images/dbs-3.png)

        可以使用如下的 SQL 语言来描述 foreign keys：

        ```sql
        Create table account 
        (account-number char(10), 
        branch-name char(15), 
        balance integer, 
        primary key (account-number), 
        foreign key (branch-name) references branch); 

        Create table depositor 
        (customer-name char(20), 
        account-number char(10), 
        primary key (customer-name, account-number), 
        foreign key (account-number) references account, 
        foreign key (customer-name) references customer);
        ```

        （btw，depositor 的 primary key 包含两个 attr）

    + Cascading Actions in SQL

        ```SQL
        Create table account ( 
            ...
        foreign key (branch-name) references branch
        [ on delete cascade ] 
        [ on update cascade ] 
        ... );
        ```

        使用 `[on delete cascade]` 允许级联删除，使用 `[on update cascade]` 允许级联更新。

        之前提及，如果两表 $r_2,r_1$ 具有「参照 $\to$ 被参照」关系，当 $r_1$ 发生删除 / 更新操作时，我们可能需要相应地修改 $r_2$ 来维持此关系。

        如果存在一条「参照 $\to$ 被参照」关系链，一个表的更改可能会导致链式反应。如果系统发现无法维护所有关系，它会取消本次 transaction。

    + Alternative to Cascading
    
        除了使用级联，我们还可以这样操作：

        + `[on delete set null]`
        + `[on delete set default]` 

        这样，当被参照关系中某个 tuple 被删除时，参照关系不会随之删除，而是设置某项为 NULL。

        Null values in foreign key attributes complicate SQL referential integrity semantics, and are best prevented using not null. If any attribute of a foreign key is null, the tuple is defined to satisfy the foreign key constraint!

    + Referential Integrity Example

        ```sql
        Create table Person
        (id char(10) primary key, 
        name varchar(12) not null, 
        age int, 
        gender char(1), 
        spouse char(10), 
        foreign key(spouse) references Person
        on update cascade on delete set NULL, 
        Check(gender in {‘f’, ‘m’}); 
        ```

        `foreign key(spouse) references Person on update cascade on delete set NULL` 是一个外键约束，指定 spouse 字段引用表内部同一表的 id 字段。
        
        这意味着 spouse 必须是表中另一条记录的 id。如果某人的配偶的记录被更新（比如配偶的 id 发生变化），那么这个人的配偶信息也会相应更新（`on update cascade`）。如果某人的配偶被删除，那么这个人的配偶信息会被设置为 NULL（`on delete set NULL`），而不是删除这条记录。

+ Assertion

    An assertion is a predicate expressing a condition that we wish the database always to satisfy, usually for complex check condition on several relations.
    
    SQL 格式：

    ```sql
    CREATE ASSERTION <assertion-name>
        CHECK <predicate>;
    ```

    When an assertion is made, the system tests it for validity **on every update** that may violate the assertion. (when the predicate is true, it is OK, otherwise report error)

    例如，对于之前的 Banking Example，要求每个分行满足其 load 总和不超过其 balance 总和。

    ```sql
    CREATE ASSERTION sum-constraint CHECK 
        (not exists (select * from branch B 
            where (select sum(amount) from loan 
            where loan.branch-name = B.branch-name) 
            > 
            (select sum(balance) from account 
            where account.branch-name = B.branch-name)))
    ```

+ Triggers（此部分和讲义不同，按照 MySQL 的语法）

    A trigger is a statement that is executed automatically by the system as a side-effect of a modification to the database.

    例子：在 Banking Example 中，如果一个用户的 balance 低于 0，则为其创建 loan 账户，并将 balance 的负值设置为 amount。

    ```sql
    CREATE TRIGGER overdraftTrigger BEFORE UPDATE ON account
        FOR EACH ROW
        BEGIN
            IF NEW.balance < 0 THEN
                INSERT INTO borrower
                (SELECT customer_name, account_number FROM depositor
                WHERE NEW.account_number = depositor.account_number);
                
                INSERT INTO loan VALUES
                (NEW.account_number, NEW.branch_name, -NEW.balance);
                
                SET NEW.balance = 0;
            END IF;
        END;
    ```

    ```
    loan(loan-number, branch-name, amount) 
    borrower(customer-name, loan-number) 
    account(account-number, branch-name, balance) 
    depositor(customer-name, account-number)
    ```

    + Trigger 触发条件

        可以选用 `insert` / `delete` / `update`。上一个例子用的 `update`。

        你也可以使用 `of` 来规定某些 attr 被修改时才触发。例如

        ```sql
        Create trigger overdraft-trigger 
            after update of balance on account …
        ```

    + AFTER vs. BEFORE

        `AFTER` 使得 trigger 里面的内容在修改生效之后进行。
        
        `BEFORE` 使得 trigger 里面的内容在修改生效之前进行。

    + FOR EACH ROW

        可理解为遍历每一个被修改的行。

    + 【FLAG】和讲义不同的点

        `REFERENCING` 语法，包括 new / old + row / table。

### Authorization

+ Basic Authorization Types

    Forms of authorization on parts of the **database**: （单表而言）
    
    1. **Read authorization** - allows reading, but not modification of data. 
    2. **Insert authorization** - allows insertion of new data, but not modification of 
    existing data. 
    3. **Update authorization** - allows modification, but not deletion of data. 
    4. **Delete authorization** - allows deletion of data.
    
    Forms of authorization to modify the **database schema**:（整个 schema 而言）

    1. **Index authorization** - allows creation and deletion of indices. 
    2. **Resources authorization** - allows creation of new relations. 
    3. **Alteration authorization** - allows addition or modifying of attributes in a relation. 
    4. **Drop authorization** - allows deletion of relations. 


+ Authorization and Views

    可以通过 Views 来限制一个用户的权限。例如，一个 clerk 只能知道每个 branch 顾客的名字，我们可以使用如下的 View：

    ```sql
    CREATE VIEW cust-loan as 
    SELECT branchname, customer-name 
    FROM borrower, loan 
    WHERE borrower.loan-number = loan.loan-number 
    ```

    同时，clerk 应当具有 `SELECT` 该视图的权限。clerk 通过以下命令获取信息：

    ```sql
    SELECT * 
    FROM cust-loan
    ```

    Creation of view does not require **resources authorization** since no real relation is being created. 

    The creator of a view gets only those privileges that provide no additional authorization beyond that he already had. E.g., If creator of view cust-loan had only read authorization on borrower and loan, he gets only read authorization on cust-loan.

+ Authorization Grant Graph

    The nodes of this graph are the users. The root of the graph is the database administrator. 

    Consider graph for update authorization on loan, then an edge $U_i \to U_j$ indicates that user $U_i$ has granted update authorization on loan to $U_j$.

    ![dbs-21](images/dbs-21.png)

    Must prevent cycles of grants with no path from the root.

    **EXAMPLE:** If DBA revokes grant from U1: 
    
    + Grant must be revoked from U4 since U1 no longer has authorization. 
    
    + Grant must not be revoked from U5, since U5 has another authorization path from DBA through U2. 

+ Security Specification in SQL

    接下来讲解具体在 SQL 语言中如何体现权限管理。

    1. 使用 GRANT 语句来赋予权限

        ```sql
        GRANT <privilege list> ON <table | view> 
        TO <user list>
        ```

        例如，`GRANT select, insert ON branch TO U1, U2, U3;` 赋予了 U1, U2, U3 对于 branch 这张表的 select, insert 权限。注意 `ON` 后面也可以是一个视图。

    2. 使用 REVOKE 语句来收回权限

        ```sql
        REVOKE <privilege list> ON <table | view> 
        FROM <user list> [restrict | cascade]
        ```

        About Restrict / Cascade: Revocation of a privilege from a user may cause other users also to lose that privilege, which is referred to as **cascading** of the revoke. With **restrict**, the revoke command fails if cascading revokes are required.

        `<privilege-list>` may be `ALL`, to revoke all privileges the revokee may hold. 
        
        If `<revokee-list>` includes `PUBLIC`, all users lose the privilege except those granted it explicitly. 
    
    3. WITH GRANT OPTION 传递权限

        例如，

        ```sql
        grant select on branch to U1 with grant option;
        ```

        赋予了 U1 对于 branch 的 select 权限，同时 U1 也可以对其它用户赋予该权限。

+ ROLES: Permitting common privileges for a class of users

    例如，

    ```sql
    Create role teller;
    Create role manager;
    Grant select on branch to teller;
    Grant update (balance) on account to teller;
    Grant all privileges on account to manager;
    Grant teller to manager;
    Grant teller to alice, bob; 
    Grant manager to avi;
    ```

    `GRANT BY CURRENT ROLE`：之前说到，用户之间可以传递权限。这个命令可以使得权限的传递是以 role 形式完成的。也就是说，如果 A 给 B 了某个权限（且没有其它人给 B 这个权限），在 A 失去权限之后 B 也会失去权限；但如果 A 是以 `GRANT BY CURRENT ROLE` 方法给出的权限，那么 B 将依然保留此权限。

+ Limitations of SQL Authorization

    1. SQL does not support authorization at a tuple level.

    2. The task of authorization in above cases falls on the application program, with no support from SQL.

    3. With the growth in Web access to databases, database accesses come primarily from application servers.

### Embedded SQL（嵌入式 SQL）

A language in which SQL queries are embedded is referred to as a **Host language (宿主语言)**, and the SQL structures permitted in the host language comprise embedded SQL.

![dbs-22](images/dbs-22.png)

+ 使用 `EXEC SQL` 和 `END_EXEC` 来标注嵌入的部分。

+ 游标 (cursor) 的作用类似于一个类。

+ `:X` 是宿主变量，可在宿主语言程序中赋值，从而将值带入 SQL。宿主变量在宿主语言中使用时不加 `:` 号。


---


## Lecture 5. E-R Model

### Introduction

+ E-R Model vs. Relation Model

    顾名思义，E-R Model 引入了「实体」的概念，将部分表格归入「描述实体」的范畴。回顾之前一张 Relation Model 用过的图：

    ![dbs-3](images/dbs-3.png)

    比如对于 borrower 表格，在 Relation Model 中它对应一个关系，在 E-R Model 中它也对应一个关系；但对于 customer 表格，在 Relation Model 中它对应一个关系，在 E-R Model 中它则对应一个实体。

    E-R Model 更为符合语义上的直觉。~~其实感觉都挺迷惑的。~~

+ Entities 实体

    一个实体具有一些 Attributes，而 Attributes 可以如下分类：

    + Simple Attributes vs. Composite Attributes (简单和复合属性)

        <p><img src="images/dbs-23.png" alt="dbs-23" width="70%"></p>

       如上图，复合属性可以被拆成若干个子属性。例如 sex 是简单属性，而 name 是复合属性。
 
    + Single-valued vs. multi-valued attributes (单值和多值属性)
    
        例如，phone-numbers 可以是多值属性。

    + Derived Attributes (派生属性)

        派生属性指的是一个属性可以被其他 **已存在的属性计算得出**。例如，当 birth date 已知，则 age 为派生属性。

+ Relationship Set 联系集

    一个联系集表示二个或多个实体集之间的关联。
    
    正式定义：对于超过 $n \ge 2$ 个实体集，我们定义一个它们之间的一个联系集为

    $$rs = \\{(e_1,e_2,\cdots,e_n) \text{ where } e_1 \in E_1, e_2 \in E_2, \cdots, e_n \in E_n \\}$$

    其中 $(e_1, e_2, \cdots, e_n)$ 称作一个 relationship, $E_i$ 是实体集。

    定义一个联系集的度数 (degree) 为其链接的实体集数目。

### E-R Diagram

接下来通过 E-R Diagram 来更深刻地认识 E-R 模型。

0. 基本表示方法

    椭圆表示属性、矩形表示实体、菱形表示关系。

1. E-R Diagram and Attributes

    对于一个实体，我们通过下图来列举其属性。下划线表示主键。

    <p><img src="images/dbs-24.png" alt="dbs-24" width="70%"></p>

    对于一个关系，有的时候它会牵一个属性。我们使用下图方式表示：

    <p><img src="images/dbs-25.png" alt="dbs-25" width="80%"></p>

2. E-R Diagram and Relationship Set

    + 自环联系集：

        在具有特殊意义时，一个实体集可以和自身产生关系。

        <p><img src="images/dbs-26.png" alt="dbs-26" width="70%"></p>

    + 考虑映射数目（一对一 / 一对多 / 多对多）：

        <p><img src="images/dbs-27.png" alt="dbs-27" width="90%"></p>

        一个例子如下：

        <p><img src="images/dbs-38.png" alt="dbs-38" width="60%"></p>

        注意：打箭头的一方表示 ONE，无箭头实线的一方表示 MANY。此处「one to many」的意思是 one instructor 可以对应 many students；但如果放到联系集中考虑的话，反而某个 instructor 会在联系集中出现多次，某个 student 则只会在联系集中出现一次。注意区分意义。

    + 全参与和部分参与：

        全参与说明对应的那一侧都在 Relationship Set 中至少出现过一次。部分参与则无此限制。

        <p><img src="images/dbs-28.png" alt="dbs-28" width="90%"></p>

    + Tips | Converting Non-Binary Relationships to Binary Form

        有的时候，非三连接联系集可以拆成若干个双连接联系集。当然大多数时候没有对错之分。

        <p><img src="images/dbs-29.png" alt="dbs-29" width="70%"></p>

### Weak Entity Sets

基本定义：没有主键的实体集称作 **弱实体集**。它需要依赖于其它实体集存在。

例如，下图中 section 是一个弱实体集。其中画下划虚线的称作 Discriminator (or Partial Key)，当 Discriminator 和其所依赖的实体集的 Primary Key 发生组合时，将可以在弱实体集中进行唯一的区分（发挥主键作用）。同时，弱实体集和其依赖的实体集之间的关系菱形需要用双实线包围。

<p><img src="images/dbs-30.png" alt="dbs-30" width="70%"></p>

section 被作为弱实体是因为它可能重复。例如，COD 课和 DBS 课都在 2024 春学期开课，同时分别具有 2 / 3 个 Section。那么在 section 中就会同时具有如下实体：

```
(1, spring, 2024)
(2, spring, 2024)
(1, spring, 2024)
(2, spring, 2024)
(3, spring, 2024)
```

必须要和 `course_id` 发生组合后，才能唯一区分：

```
(COD, 1, spring, 2024)
(COD, 2, spring, 2024)
(DBS, 1, spring, 2024)
(DBS, 2, spring, 2024)
(DBS, 3, spring, 2024)
```

可能的疑惑 1：为什么 section 中不直接存 `(1, spring, 2024)`，`(2, spring, 2024)` 和 `(3, spring, 2024)`？这是因为 section 中的实体有其 **意义**（品一下，结合 `course_id + section`），如果删去重复部分的话则会失去对应的意义，也难以恢复和 course 实体的组合（比如就不确定 COD 有几个 section 了）。

可能的疑惑 2：为什么不直接把 section 存在 course 里面？这是因为 course 表格也有其意义，比如我只希望调用课程信息，两实体合在一起会产生很多的信息冗余。


### An E-R Example

<p><img src="images/dbs-31.png" alt="dbs-31"></p>

### 扩展 E-R 模型性质

1. Specialization

    类似父类和子类。

    <p><img src="images/dbs-32.png" alt="dbs-32" width="40%"></p>

    注意右上那种箭头表示一个人可以同时是 employee & student；左下那种箭头表示一个 employee 不能同时作为 instructor / secretary 存在。

    此外还有完全泛化 / 部分泛化的定义。字面意思很好理解。

    <p><img src="images/dbs-33.png" alt="dbs-33" width="50%"></p>

2. Aggregation

    左边 manager 同时 manages 三个，太麻烦了。所以搞成右边的格式——

    <p><img src="images/dbs-34.png" alt="dbs-34" width="100%"></p>



### 将 E-R 模型转化为表格

+ 基本方法

    实体转表格，联系集转表格。

    实体的属性有的是复合 / 多值的，这些都需要展开。不过，另外一种处理方法是用多个表格描述一个实体。

    联系集则只需要把 attrs 设置成所联系的实体集的主键。此外还要记得带上那些绑定在联系集上面的属性。

+ 处理弱实体

    弱实体也需要出一张表格，将其所依赖的强实体的 Primary Key 和弱实体的 Discriminator 联合一下作为 attrs 即可。

+ 缩表

    1. 对 1-n 联系，可将联系所对应的表，合并到对应「多」端实体的表中。

        <p><img src="images/dbs-35.png" alt="dbs-35" width="100%"></p>

        当然，对 1-1 联系，合并到哪一侧都是可以的。

    2. 联系弱实体集及其标识性实体集的联系集对应的表是冗余的，即对应 identifying relationship 的表是多余的。

        <p><img src="images/dbs-36.png" alt="dbs-36" width="100%"></p>

+ 处理泛化

    <p><img src="images/dbs-37.png" alt="dbs-37" width="50%"></p>

    两种处理方式都是允许的。上面的信息更简洁，下面的话在特定情况查询更方便。


## Lecture 6. Relational Database Design

### First Normal Form 第一范式

A relational schema R is in first normal form (1NF) if **the domains of all attributes of R are atomic**.

违反原子性的 attrs:

+ Composite attributes (复合属性) --- set of names 
+ Multi-value attribute (多值属性) --- a person’s phones 
+ Complex data type (过于复杂的类型) --- object-oriented

简单来说，atomic 要求每一个 attr 都 **不可分割**。

+ 不符合 1NF（例如多值属性），有以下解决方案：

    1. Use multi fields

        e.g., `person(pname, ..., phon1, phon2, phon3, ...)`

    2. Use a separate table

        e.g., `phone(pname, phone)`

    3. Use a single field

        e.g., `person(pname, ..., phones, ...)`

+ 如何理解 ATOMIC（根据意义确定）

    Atomicity is actually a property of how the elements of the domain are used. 
    
    E.g., Strings would normally be considered indivisible. 
    
    Suppose that students are given roll numbers which are strings of the form 
    CS0012 or EE1127.
    
    If the first two characters are extracted to find the department, **the domain of roll numbers is not atomic**. 

### BCNF and 3NF BC 范式与第三范式

#### 1 / Pitfalls in Relational Database Design


+ A Pitfall in Relational Database Design

    考虑 Banking Example 中的一张表

    ```
    Lending-schema = (branch-name, branch-city, assets, customer-name, loan-number, amount)
    ```

    <p><img src="images/dbs-39.png" alt="dbs-39" width="70%"></p>

    branch-name, branch-city, assets 发生了大量重复。这是一个冗余的设计。

    事实上，除了浪费空间，这种设计还会导致许多问题。比如一个 branch 没有贷款客户，我们该如何存储这个 branch 的信息？

+ Decomposition

    一个分解方法是，将 `(ABCD)` 分解为 `(AB)` 和 `(BCD)`，或者分解为 `(ACD)` 和 `(ABD)`，等等。

    【定义】**无损连接分解**：假设某 schema $(R)$ 被分解为 $(R_1,R_2) \ (R_1 \cup R_2 = R)$，并且对于所有可能的 relation $r$，有 $r = \Pi_{R_1}(r) \bowtie \Pi_{R_2}(r)$，则认为 $R \to R_1+R_2$ 是无损的。


#### 2 / Function Dependencies

+ Basic Def

    The functional dependency $\alpha \to \beta$ holds on $R$ if and only if for any legal relations $r(R)$, whenever any two tuples $t_1$ and $t_2$ of $r$ agree on:

    The attributes $\alpha$, they also agree on the attributes $\beta$, i.e., 

    $$t_1[\alpha]=t_2[\alpha] \implies t_1[\beta]=t_2[\beta] $$

    简单来说，函数依赖关系 $\alpha \to \beta$ 说明 $\alpha$ 能唯一地决定 $\beta$。例如，对于这样一个表格，有 $B \to A$ 成立。

    |A|B|
    |---|---|
    |1|4|
    |1|5|
    |3|7|

    不难发现，若 $K$ 是 relation $r(R)$ 的 super key，那么 $K \to R$；若 $K$ 是 relation $r(R)$ 的 candidate key，那么还需要有：不存在 $\alpha \subset K$，满足 $\alpha \to R$。

    根据某个 $r(R)$ 的实例似乎可以判断某组函数依赖 $\alpha \to \beta$ 是否成立，然而这样往往是不准确的，还是要按 $R$ 的意义来。

+ Trivial / Non-Trivial FD

    平凡的函数依赖：$\alpha \to \beta$，且 $\beta \subseteq \alpha$。

    非平凡的函数依赖：$\alpha \to \beta$，且 $\beta \nsubseteq \alpha$。

+ Closure 闭包

    函数依赖关系 $F$ 的闭包记作 $F^{+}$。其意义是 $F \implies F^{+}$，且 $F^{+}$ 是极大的。

    如何寻找函数依赖 $F$ 的闭包，依赖下列定律（下面三个定律已经完备）：

    + 自反律：If $\beta \subseteq \alpha$, then $\alpha \to \beta$

    + 增补律：If $\alpha \to \beta$, then $\gamma\alpha \to \gamma\beta$

    + 传递律：If $\alpha \to \beta$ and $\beta \to \gamma$, then $\alpha \to \gamma$

    为了加速寻找 $F \implies F^{+}$，还可以使用以下三个引申的定律：

    + 合并律：If $\alpha \to \beta$ and $\alpha \to \gamma$, then $\alpha \to \beta\gamma$

    + 分解律：If $\alpha \to \beta\gamma$, then $\alpha \to \beta$ and $\alpha \to \gamma$

    + 伪传递律：If $\alpha \to \beta$ and $\gamma\beta \to \delta$, then $\alpha\gamma \to \delta$

+ 属性集的闭包

    计算 $F \implies F^{+}$ 是繁琐的。有的时候我们只关心某个属性集 $\alpha$ 可以决定哪些属性。称这些被 $\alpha$ 决定的属性构成的集合为 $\alpha^{+}$。

    【理解检测 1】$\alpha \to \beta$ is in $F^{+}$ means that $\beta \subseteq \alpha^{+}$.

    【理解检测 2】对于所有的 $\alpha \subseteq R$，将函数依赖 $\alpha \to \beta \ (\beta \in \alpha^{+})$ 组合在一起，就构成了 $F^{+}$。

+ 正则覆盖

    上面是说怎样「扩大」函数依赖集合，接下来讨论如何「缩减」。可以证明，对于一组函数依赖集合 $F$，总能找到一个极小的 $F_c$，使得两者等价。

    为了找到 $F_c$，经验上可以应用三条方法：

    1. 直接删去可被其它函数依赖推出的函数依赖。

        例如 $F=\\{A \to C, A \to B, B \to C\\} $，显然 $A \to C$ 可以走人了。

    2. 删去左侧冗余。

        如 $F=\\{A \to B, B \to C, AC \to D\\} $ 可缩为 $F=\\{A \to B, B \to C, A \to D\\} $。这个过程可以被数学描述为，当 $A \in \alpha$，并且

        $$F \implies (F-\\{\alpha \to \beta \\}) \cup \\{(\alpha - A) \to \beta \\}$$

        那么 $\alpha \to \beta$ 可以删去左侧冗余项 $A$。注意上面虽然只有 $\implies$，但实际上 $\implies$ 就可以推出 $\iff$，所以左右两侧实际上就是等价的，这符合「缩减」的要求。事实上，由于 $F \implies (F-\\{\alpha \to \beta \\})$ 是必然的，所以只要满足

        $$F \implies \\{(\alpha - A) \to \beta \\}$$

        就可以执行「缩减」。

    3. 删去右侧冗余。

        原理几乎同上，不再赘述。

#### 3 / Decomposition

回到第 1 部分。我们希望对表格进行分解，使其成为一个「更好的设计」。具体来说，我们有以下三个层次的目标（假设 $R \to R_1,R_2,\cdots,R_n$）：

1. 无损连接分解。这是最基本的。

2. 依赖保持。

3. 分解后的表格 $R_1,R_2,\cdots,R_n$ 满足 BCNF 或 3NF。

一个一个说：

+ 无损连接分解（考虑一分为二的情况）

    可以证明，无损连接分解的 **充要条件** 是，分解后的二个子模式的共同属性必须是 $R_1$ 或 $R_2$ 的码。或者说，要么有 $R_1 \cap R_2 \to R_1$，要么有 $R_1 \cap R_2 \to R_2$。证明可以画一个模拟表格来证。

+ 依赖保持

    假设某种分解是 $R \to R_1,R_2,\cdots,R_n$。设 $R$ 所具有的函数依赖集合为 $F^{+}$，定义 $F_i \subseteq F^{+}$，其中 $F_i$ 包含且仅包含箭头两侧都属于 $R_i$ 的函数依赖。

    如果有

    $$(F_1 \cup F_2 \cup \cdots \cup F_n)^{+}=F^{+}$$

    则认为这种分解满足 **依赖保持** 性质。

    例如，$R=(A,B,C)$，$F=\\{A \to B, B \to C\\}$，有以下两种分解方案：

    【方案一】$R_1=\\{A,B\\},R_2=\\{B,C\\}$

    【方案二】$R_1=\\{A,B\\},R_2=\\{A,C\\}$

    两方案都满足无损连接分解，但只有方案一满足依赖保持。

+ BCNF / 3NF

    下一节细说。

#### 4 / BCNF

对于 relation $r(R)$，若其函数依赖集合为 $F$，对于所有 $(\alpha \to \beta) \in F^{+}$，若总满足两者其一：

1. $(\alpha \to \beta)$ 是平凡的

2. $\alpha$ 是 $R$ 的 Super Key（亦即：$R = \alpha^{+}$）

则认为 $r(R)$ 符合 BCNF。

例如，若 $R=(A,B,C)$，$F=\\{A \to B, B \to C\\}$，则 $r(R)$ 并不符合 BCNF。

+ BCNF 的判定

    可以证明，**只需根据函数依赖集合 $F$ 判断是否满足 BCNF 即可；不必转化成 $F^{+}$ 再进行判断**。换句话说，把上面的定义语言中的 $(\alpha \to \beta) \in F^{+}$ 替换成 $(\alpha \to \beta) \in F$ 进行判断即可。

    在分解的情景下则略有差别，如 $R \to R_1,R_2,\cdots,R_n$。若要判断 $R$ 是否符合 BCNF，可以直接拿 $F$ 判断；但若要判断 $R_1,R_2,\cdots,R_n$ 是否符合 BCNF，则必须使用 $F_i \subseteq F^{+}$ 来进行判断。

    <p><img src="images/dbs-40.png" alt="dbs-40" width="70%"></p>

+ BCNF 分解

#### 5 / 3NF