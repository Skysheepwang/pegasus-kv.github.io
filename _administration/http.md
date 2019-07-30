---
title: HTTP接口
layout: page
show_sidebar: false
menubar: administration_menu
---

# 功能目标

Pegasus为meta服务器和replication服务器提供了HTTP接口，用于查看集群相关信息，查询服务器信息等。

# 接口介绍

接口共分为服务器信息查询、表管理和节点管理三类，其中服务器信息查询类中的指令同时支持replication服务器和meta服务器的调用，其它指令支持meta服务器。

当访问备用meta服务器的表管理和节点管理类接口时，由于相关信息保存在主meta服务器中，因此会自动重定向至主meta服务器对应的接口。

所有接口均返回JSON格式。

## 服务器信息查询类

- **ip/port/version**

  - 功能：获取应用的版本号和GIT_COMMIT代码

  - 示例：

    - URL:：127.0.0.1:34801/version
    - 返回：

    ```json
    {
    	Version: {
    		Version: "1.12.SNAPSHOT",
    		GitCommit: "f9741e8d650a35439b22bc9c943ec72ecf61c675"
    	}
    }
    ```

    

- **ip/port/recentStartTime**

  - 功能：  获取进程启动时间

  - 示例：

    - URL：127.0.0.1:34801/recentStartTime
    - 返回：

    ```json
    {
    	RecentStartTime: {
    		RecentStartTime: "2019-07-30 14:04:25"
    	}
    }
    ```

    

## 表管理类

- **ip:port/meta/app**

  - 功能：获取某个表的基本信息。

  - 参数：

    - app_name

      ​	待查的app名称

  - 示例：

    - URL：127.0.0.1:34601/meta/app?app_name=temp
    - 返回：

    ```json
    {
    	general: {
    		app_name: "temp",
    		app_id: "1",
    		partition_count: "8",
    		max_replica_count: "3"
    	}
    }
    ```



- **ip:port/meta/apps**

  - 功能：获取所有表的列表。

  - 示例：

    - URL：127.0.0.1:34601/meta/apps
    - 返回：

    ```json
    {
    	general_info: {
    		1: {
    			app_id: "1",
    			status: "AVAILABLE",
    			app_name: "temp",
    			app_type: "pegasus",
    			partition_count: "8",
    			replica_count: "3",
    			is_stateful: "true",
    			create_time: "2019-07-30 14:04:26.000",
    			drop_time: "-",
    			drop_expire: "-",
    			envs_count: "0"
    		}
    	},
    	summary: {
    		total_app_count: "1"
    	}
    }
    ```

    

## 节点管理类

- **ip:port/meta/cluster**

  - 功能：获取集群基本信息。

    ​			集群信息主要包含：

    ​				- meta_server、zookeeper的节点信息。

    ​				- meta_function_level：负载均衡策略。

    ​				- balance_operation_count：负载均衡操作统计，包括move_pri、move_pri、copy_sec、				   total。

    ​				- primary_replica_count_stddev：负载均衡衡量指标。

    ​				- total_replica_count_stddev：负载均衡衡量指标。

  - 返回格式：

    ```json
    {
    	cluster_info: {
    		meta_servers: 			"10.239.35.160:34601,10.239.35.160:34602,10.239.35.160:34603",
    		primary_meta_server: "10.239.35.160:34601",
    		zookeeper_hosts: "127.0.0.1:22181",
    		zookeeper_root: "/pegasus/onebox/10.239.35.160",
    		meta_function_level: "lively",
    		balance_operation_count: "move_pri=0,copy_pri=0,copy_sec=0,total=0",
    		primary_replica_count_stddev: "0.47",
    		total_replica_count_stddev: "0.00"
    	}
    }
    ```

    

- **ip:port/meta/nodes**

  - 功能：获取replica节点列表，以IP地址表示各个节点，并输出基本信息。

  - 返回格式：

    ```json
    {
    	details: {
    		10.239.35.160:34801: {
    			address: "10.239.35.160:34801",
    			status: "ALIVE"
    		},
    		10.239.35.160:34802: {
    			address: "10.239.35.160:34802",
    			status: "ALIVE"
    		},
    		10.239.35.160:34803: {
    			address: "10.239.35.160:34803",
    			status: "ALIVE"
    		}
    	},
    	summary: {
    		total_node_count: "3",
    		alive_node_count: "3",
    		unalive_node_count: "0"
    	}
    }
    ```

    