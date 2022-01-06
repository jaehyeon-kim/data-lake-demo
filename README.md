# data-lake-demo

Data lake demo using change data capture (CDC) on AWS.

- [Part 1 Database and Local Development](https://cevo.com.au/post/data-lake-demo-using-cdc-part-1/)
- [Part 2 CDC with Amazon MSK](https://cevo.com.au/post/data-lake-demo-using-cdc-part-2/)
- [Part 3 Hudi Table and Dashboard Creation](https://cevo.com.au/post/data-lake-demo-using-cdc-part-3/)

## Architecture

![architecture](./images/architecture.png)

1. Employing the [transactional outbox pattern](https://microservices.io/patterns/data/transactional-outbox.html), the source database publishes change event records to the CDC event table. The event records are generated by triggers that listen to insert and update events on source tables.
2. CDC is implemented in a streaming environment and [Amazon MSK](https://aws.amazon.com/msk/) is used to build the streaming infrastructure. In order to process the real-time CDC event records, a source and sink connectors are set up in [Amazon MSK Connect](https://aws.amazon.com/msk/features/msk-connect/). The [Debezium connector for PostgreSQL](https://debezium.io/documentation/reference/stable/connectors/postgresql.html) is used as the source connector and the [Lenses S3 connector](https://lenses.io/blog/2020/11/new-kafka-to-S3-connector/) is used as the sink connector. The sink connector pushes messages to a S3 bucket.
3. [Hudi DeltaStreamer](https://hudi.apache.org/docs/writing_data/#deltastreamer) is run on [Amazon EMR](https://aws.amazon.com/emr/). As a spark application, it reads files from the S3 bucket and upserts Hudi records to another S3 bucket. The Hudi table is created in the AWS Glue Data Catalog.
4. The Hudi table is queried in [Amazon Athena](https://aws.amazon.com/athena/) while the table is registered in the AWS Glue Data Catalog.
5. Dashboards are created in [Amazon Quicksight](https://aws.amazon.com/quicksight/) where the dataset is created using Amazon Athena.
