# elasticache-redis CfHighlander component

## Requirements

## Parameters

| Name | Use | Default | Global | Type | Allowed Values |
| ---- | --- | ------- | ------ | ---- | -------------- |
| EnvironmentName | Tagging | dev | true | String
| EnvironmentType | Tagging | development | true | String | ['development','production']
| DnsDomain | Zone to launch redis record in | | true | String
| VPCId | ID of the VPC to launch Redis in |  | false | AWS::EC2::VPC::Id
| InstanceType | The compute and memory capacity of the nodes in the node group (shard) | t3.small | false | String
| Subnets | list of subnet ciders for the redis cluster |  | false | CommaDelimitedList
| SnapshotName | The name of a snapshot from which to restore data into the new replication group |  | false | String
| SnapshotArns | A list of ARNs that uniquely identify the Redis RDB snapshot files stored in S3 | | false | String
| SnapshotRetentionLimit | The number of days for which ElastiCache retains automatic snapshots before deleting them | | false | Integer
| NumNodeGroups | Specifies the number of node groups (shards) for this Redis replication group | 1 | false | Integer |
| ReplicasPerNodeGroup | An optional parameter that specifies the number of replica nodes in each node group (shard) | 0 | false | Integer | [0, 1, 2, 3, 4, 5]
| NumCacheClusters | The number of clusters this replication group initially has | 2 | false | Integer | [1, 2, 3, 4, 5, 6]

## Configuration

### Redis Mode

**Redis (Clustered Mode Enabled)**

To create a replication group with Redis (Clustered Mode Enabled), you need to ensure these two configuration options are set. 

```yaml
cluster_enabled: true
automatic_failover: true
```

This is the default option

**Redis (Clustered Mode Disabled)**

To create a replication group with Redis (Clustered Mode Disabled), simply set the below configuration option. Automatic failover is optional for Redis (Clustered Mode Disabled), but defaults to on.

```yaml
cluster_enabled: false
```


### Replication Mode

There are two replication modes to choose from when creating Redis. The default mode uses node groups.

**Node Groups**

To explicitly set config to use node groups:

```yaml
replication_mode:  node_group
```

This will then enable the following two parameters:

- **NumNodeGroups** Defaults to 1, but specifies the number of node groups (shards) for this Redis replication group.
- **ReplicasPerNodeGroup** Defaults to 0, that specifies the number of replica nodes in each node group (shard). Valid values are 0 to 5.

**Cache Clusters**

To explicitly set config to use cache clusters:

```yaml
replication_mode:  cache_clusters
```

This will then enable the following parameter:

- **NumCacheClusters** Defaults to 2, The number of clusters this replication group initially has. Valid values are 1 to 6. If AutomaticFailoverEnabled is true, the value of this parameter must be at least 2.

#### Restoring from Snapshots

**Native Snapshot Restore**

To restore from a snapshot taken by the service, simply set the configuration below:

```yaml
snapshot_restore_type: native
```

This will enable the below parameter:

- **SnapshotName** The name of a snapshot from which to restore data into the new replication group

### S3 Snapshot Restore

To restore from RDB snapshot files stored in S3, set the configuration below:

```yaml
snapshot_restore_type: s3
```

This will enable the below parameter:

- **SnapshotArns** A list of ARNs that uniquely identify the Redis RDB snapshot files stored in S3

