---
title: 【笔记】计算机组成 (Part IV)
category: Notes
date: 2024-01-01
---

## Chapter 5 Memory Hierarchy

### Basic Memory Types

+ SRAM

    六个晶体管。static 是指这种存储器只需要保持通电，里面的数据就可以永远保持。但是当断点之后，里面的数据仍然会丢失。
    
    SRAM 速度较快，但是成本更高。所以像诸如 CPU 的高速缓存，才会采用 SRAM。


+ DRAM

    一个电容加一个晶体管。由于 DRAM 使用电容存储，所以必须隔一段时间 refresh 一次，否则因为不断的微小漏电可能导致数据丢失。
    
    DRAM 速度较慢，但是成本更低。所以可以用于作为 main memory。


