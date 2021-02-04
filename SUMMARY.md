# Summary

* [Introduction](README.md)

* [大纲](index.md)

* [第一部分 基础知识](part0.基础知识/1.汇编基础.md)
    * [汇编基础](part0.基础知识/1.汇编基础.md)
    * [内存](part0.基础知识/2.内存.md)

* [第二部分 进入main函数之前的初始化](part1.进入main函数之前的初始化/1.进入main函数之前的初始化.md)
    * [进入main函数之前的初始化](part1.进入main函数之前的初始化/1.进入main函数之前的初始化.md)

* [第三部分 退出goroutine](part2.退出goroutine/1.退出goroutine.md)
    * [退出goroutine](part2.退出goroutine/1.退出goroutine.md)

* [第四部分 调度]()
    * [1.调度策略](part3.调度/1.调度策略/1.1.从本地队列或全局队列获取goroutine.md)
        * [从本地队列或全局队列获取goroutine](part3.调度/1.调度策略/1.1.从本地队列或全局队列获取goroutine.md)
        * [盗取goroutine从其他队列](part3.调度/1.调度策略/1.2.盗取goroutine从其他队列.md)
    * [2.调度的时机](part3.调度/2.调度的时机/2.1.主动调度.md)
        * [主动调度](part3.调度/2.调度的时机/2.1.主动调度.md)
        * [被动调度](part3.调度/2.调度的时机/2.2.被动调度.md)
        * [运行用户代码时间过长调度](part3.调度/2.调度的时机/2.3.1.运行用户代码时间过长调度.md)
        * [系统调用收尾,如果从系统调用返回,如何重新得到P](part3.调度/2.调度的时机/2.3.2.系统调用收尾,如果从系统调用返回,如何重新得到P.md)
    * [3.调度循环](part3.调度/3.调度循环/3.3.调度循环.md)
        * [调度循环](part3.调度/3.调度循环/3.3.调度循环.md)
    * [总结](part3.调度/总结.md)



view_manager_salecenter_user_sub_follow_up_record

 SELECT basic.id,
    basic.sale_center_user_id,
    basic.manager_user_id,
    basic.communication_mode,
    basic.communication_result,
    basic.content,
    basic.next_follow_up_time,
    basic.follow_up_status,
    basic.tag_important_custom,
    basic.reback_reason,
    basic.quick_note,
    basic.created_time,
    basic.updated_time,
    basic.is_deleted
   FROM (( SELECT manager_salecenter_user_sub_follow_up_record.sale_center_user_id,
            max(manager_salecenter_user_sub_follow_up_record.id) AS id
           FROM manager_salecenter_user_sub_follow_up_record
          GROUP BY manager_salecenter_user_sub_follow_up_record.sale_center_user_id) sub
     JOIN manager_salecenter_user_sub_follow_up_record basic ON (((basic.sale_center_user_id = sub.sale_center_user_id) AND (basic.id = sub.id))))









