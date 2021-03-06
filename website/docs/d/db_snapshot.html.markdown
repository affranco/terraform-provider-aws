---
subcategory: "RDS (Relational Database)"
layout: "aws"
page_title: "AWS: aws_db_snapshot"
description: |-
  Get information on a DB Snapshot.
---

# Data Source: aws_db_snapshot

Use this data source to get information about a DB Snapshot for use when provisioning DB instances

~> **NOTE:** This data source does not apply to snapshots created on Aurora DB clusters.
See the [`aws_db_cluster_snapshot` data source](/docs/providers/aws/d/db_cluster_snapshot.html) for DB Cluster snapshots.

## Example Usage

```terraform
resource "aws_db_instance" "prod" {
  allocated_storage    = 10
  engine               = "mysql"
  engine_version       = "5.6.17"
  instance_class       = "db.t2.micro"
  name                 = "mydb"
  username             = "foo"
  password             = "bar"
  db_subnet_group_name = "my_database_subnet_group"
  parameter_group_name = "default.mysql5.6"
}

data "aws_db_snapshot" "latest_prod_snapshot" {
  db_instance_identifier = aws_db_instance.prod.id
  most_recent            = true
}

# Use the latest production snapshot to create a dev instance.
resource "aws_db_instance" "dev" {
  instance_class      = "db.t2.micro"
  name                = "mydbdev"
  snapshot_identifier = data.aws_db_snapshot.latest_prod_snapshot.id

  lifecycle {
    ignore_changes = [snapshot_identifier]
  }
}
```

## Argument Reference

~> **NOTE:** One of either `db_instance_identifier` or `db_snapshot_identifier` is required.

The following arguments are supported:

* `most_recent` - (Optional) If more than one result is returned, use the most
recent Snapshot.

* `db_instance_identifier` - (Optional) Returns the list of snapshots created by the specific db_instance

* `db_snapshot_identifier` - (Optional) Returns information on a specific snapshot_id.

* `snapshot_type` - (Optional) The type of snapshots to be returned. If you don't specify a SnapshotType
value, then both automated and manual snapshots are returned. Shared and public DB snapshots are not
included in the returned results by default. Possible values are, `automated`, `manual`, `shared`, `public` and `awsbackup`.

* `include_shared` - (Optional) Set this value to true to include shared manual DB snapshots from other
AWS accounts that this AWS account has been given permission to copy or restore, otherwise set this value to false.
The default is `false`.

* `include_public` - (Optional) Set this value to true to include manual DB snapshots that are public and can be
copied or restored by any AWS account, otherwise set this value to false. The default is `false`.


## Attributes Reference

In addition to all arguments above, the following attributes are exported:

* `id` - The snapshot ID.
* `allocated_storage` - Specifies the allocated storage size in gigabytes (GB).
* `availability_zone` - Specifies the name of the Availability Zone the DB instance was located in at the time of the DB snapshot.
* `db_snapshot_arn` - The Amazon Resource Name (ARN) for the DB snapshot.
* `encrypted` - Specifies whether the DB snapshot is encrypted.
* `engine` - Specifies the name of the database engine.
* `engine_version` - Specifies the version of the database engine.
* `iops` - Specifies the Provisioned IOPS (I/O operations per second) value of the DB instance at the time of the snapshot.
* `kms_key_id` - The ARN for the KMS encryption key.
* `license_model` - License model information for the restored DB instance.
* `option_group_name` - Provides the option group name for the DB snapshot.
* `source_db_snapshot_identifier` - The DB snapshot Arn that the DB snapshot was copied from. It only has value in case of cross customer or cross region copy.
* `source_region` - The region that the DB snapshot was created in or copied from.
* `status` - Specifies the status of this DB snapshot.
* `storage_type` - Specifies the storage type associated with DB snapshot.
* `vpc_id` - Specifies the ID of the VPC associated with the DB snapshot.
* `snapshot_create_time` - Provides the time when the snapshot was taken, in Universal Coordinated Time (UTC).
