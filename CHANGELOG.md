Unreleased Changes
------------------

2.2.0 (2015-11-12)
------------------

* Feature - Aws::RDS - Adds support for cross-account snapshot sharing and
  modifying DB instance visibility.

* Upgrading - Aws::S3::Client - All `Aws::S3::Client` instances will default to
  using Signature Version 4 in all regions. This replaces the behavior where a
  subset of regions used the 's3' signer and would attempt to upgrade only when
  encountering signing errors.

  If you wish to continue using the 's3' signer, without auto-upgrade logic, you
  can manually choose to use the 's3' signer at client creation time:

  ```ruby
  client = Aws::S3::Client.new(signature_version: 's3')
  ```

* Issue - Aws::EC2 - Fix for `:instance_exists` waiter. Instances that have been
  terminated for a long period of time could fail the previous waiter definition
  with a false positive.

  See [related GitHub pull request #985](https://github.com/aws/aws-sdk-ruby/pull/985).

* Upgrading - Resources - All batch resource operations have been renamed to make
  it clear that they operate in batches on the entire collection.

  * All batch operations are now prefixed with `batch_`, e.g. `#start`
    is now `#batch_start`.

  * All batch operations named "delete" or "terminate" are now suffixed
    with a `!`, e.g. `#delete` is now `#batch_delete!`

  For backwards compatibility, existing batch methods still respond
  to their old names with a deprecation warning. New batch operations
  will strictly follow the new conventions.

2.1.36 (2015-11-10)
------------------

* Feature - Aws::APIGateway - Adds support for stage variables. Stage variables
  allow customers to define environment variables on each stage running their
  API configuration. The API configuration can then reference these variables to
  load values at runtime.

2.1.35 (2015-11-03)
------------------

* Feature - Aws::DeviceFarm - DeviceFarm is adding APIs to manage projects,
  device pools, new operations include:

  * UpdateDevicePool
  * DeleteDevicePool

2.1.34 (2015-11-02)
------------------

* Feature - Aws::IAM - Include support for simulating resource-level scenarios.

2.1.33 (2015-10-28)
------------------

* Issue - Aws::APIGateway - Resolved an issue that prevented users from calling
  `Aws::APIGateway::Client#put_integration`.

2.1.32 (2015-10-26)
------------------

* Feature - Aws::SSM - API update for Amazon Simple Systems Management Service.

* Feature - Aws::APIGateway - Added support for Amazon API Gateway.

  See [related GitHub issue #884](https://github.com/aws/aws-sdk-ruby/issues/884).

2.1.31 (2015-10-22)
------------------

* Feature - Aws::AutoScaling - The Amazon Auto Scaling service now allows you to
  create launch configurations that include encrypted Amazon Elastic Block Store
  (EBS) volumes.

* Feature - Aws::IAM - Adds support for resource-based policies in the AWS IAM
  policy simulator.

* Feature - Aws::EC2 - Added helpful error messages when configuring an
  `Aws::EC2::Client` with region which is actually an availability zone.
  This will prevent users from getting unhelpful networking errors.

  ```ruby
  Aws::EC2::Client.new(region: 'us-east-1a')
  #=> raises ArgumentError: :region should be a region name, not an availability zone name; try `us-west-2' instead of `us-west-2a'
  ```

  See [related GitHub issue #966](https://github.com/aws/aws-sdk-ruby/issues/966).

* Issue - Aws::InstanceProfileCredentials - Now retries errors
  raised while attempting to parse the expiration time from
  instance metadata credentials.

  See [related GitHub issue #880](https://github.com/aws/aws-sdk-ruby/issues/880).

* Issue - Aws::S3 - The `#copy_from` and `#copy_to` methods of `Aws::S3::Object`
  will now correctly encode URL un-safe characters as required by Amazon S3.
  Without the fix, these calls fail with a signature error.

  ```ruby
  s3 = Aws::S3::Resource.new
  obj = s3.bucket('target-bucket').object('target-key')

  # the key will now correctly be encoded as 'unescaped/key%20path'
  obj.copy_from(bucket:'source-bucket', key:'unescaped/key path')

  # the key will now correctly be encoded as 'unescaped/key%20path'
  src = S3::Object.new('source-bucket', 'unescaped/key path')
  obj.copy_from(src)

  # the key will now correctly be encoded as 'unescaped/key%20path'
  obj.copy_from('source-bucket/unescaped/key path')
  ```

  If you have previously worked around this issue, you should remove your
  code that URL encodes the key.

  See [related GitHub issue #944](https://github.com/aws/aws-sdk-ruby/issues/944).

* Feature - Aws::S3 - The `#copy_from` and `#copy_to` methods of `Aws::S3::Object`
  now accept the source or target bucket and key as a hash with mixed
  options.

  ```ruby
  # old format
  obj.copy_from({ bucket: 'source-bucket', key: 'source-key' }, { multipart_copy: true })

  # new format
  obj.copy_from(bucket: 'source-bucket', key: 'source-key', multipart_copy: true)
  ```

  Passing two hashes is still valid.

  See [related GitHub issue #963](https://github.com/aws/aws-sdk-ruby/issues/963).

* Feature - Aws::S3 - `Aws::S3::Client#put_object` now accepts closed files.

  See [related GitHub issue #939](https://github.com/aws/aws-sdk-ruby/pull/939).

* Issue - Aws::S3 - The `Aws::S3::Object#copy_from` no longer modifies the
  given options hash.

  See [related GitHub issue #947](https://github.com/aws/aws-sdk-ruby/issues/947).

2.1.30 (2015-10-15)
------------------

* Feature - Aws::KMS - This release of AWS Key Management Service introduces the
  ability to delete customer master keys (CMKs), including two new APIs for
  scheduling and canceling key deletion.

* Feature - Aws::S3 - Adds support for Server-Side Encryption (AES256) to
  `Aws::S3::Client#upload_part`.

* Issue - Aws::EC2 - Resolved an issue where `Aws::EC2::Client#copy_snapshot`
  would not compute the required presigned url when the `:destination_region`
  was populated.

  See [related GitHub issue #928](https://github.com/aws/aws-sdk-ruby/issues/928).

* Issue - Aws::CloudFormation - Added support for rollback states to the
  `Aws::CloudFormation::Client#wait_until` `:stack_create_complete` waiter.

  See [related GitHub issue #931](https://github.com/aws/aws-sdk-ruby/issues/931).

* Feature - Aws::AssumeRoleCredentials - Added support for passing
  `Aws::STS::Client` configuration options to `Aws::AssumeRoleCredentials#initialize`.
  This allows you to configure things such as `:region`, `:logger`, etc without
  having to pass an `Aws::STS::Client`.

  See [related GitHub issue #957](https://github.com/aws/aws-sdk-ruby/issues/957).

* Issue - Aws::S3 - Resolved an issue with pre-signed URLs generated
  with `virtual_host: true`.

  See [related GitHub issue #938](https://github.com/aws/aws-sdk-ruby/issues/938).

  See [related GitHub pull-request #940](https://github.com/aws/aws-sdk-ruby/pull/940).

2.1.29 (2015-10-08)
------------------

* Feature - Aws::ECS - Task definitions now support more Docker
  options hostname, Docker labels, working directory, networking
  disabled, privileged execution, read-only root filesystem, DNS
  servers, DNS search domains, ulimits, log configuration, extra
  hosts (hosts to add to /etc/hosts), and security options (for
  MLS systems like SELinux).

* Feature - Aws::IoT - Added support for AWS IoT control and data plane
  services.

* Feature - Aws::Lambda - Added support for function versioning.

2.1.28 (2015-10-07)
------------------

* Feature - Aws::Inspector - Added support for the new Amazon Inspector
  service. Amazon Inspector is a new service from AWS that identifies
  security issues in your application deployments. Use Inspector with
  your applications to assess your security posture and identify areas
  that can be improved. Inspector works with your EC2 Instances to
  monitor activity in the applications and system.

* Feature - Aws::KinesisFirehose - Added support Amazon Kinesis Firehose.
  Amazon Kinesis Firehose is a fully managed service for ingesting data
  streams directly into AWS data services such as Amazon S3 and Amazon Redshift.

* Feature - Aws::MarketplaceCommerceAnalytics - Added support for the new AWS
  Marketplace Commerce Analytics service.

* Feature - Aws::Kinesis - Two new Amazon Kinesis APIs that allow customers to
  choose how long their data records are stored in their Amazon Kinesis streams.
  Customers can modify a their streams to store records from 1 to 7 days, in
  increments of 1 day.

* Feature - Aws::Config - Added support for Config Rules.

2.1.27 (2015-10-06)
------------------

* Feature - Aws::WAF - Adds SDK support for AWS WAF, a web application firewall
  that helps protect your web applications from common web exploits that could
  affect application availability, compromise security, or consume excessive
  resources.

* Feature - Aws::EC2 - Adds "Spot Blocks" for Amazon Elastic Compute Cloud.

* Feature - Aws::CloudFront - Adds support for adding WebACLIds to CloudFront
  distributions.

2.1.26 (2015-10-01)
------------------

* Feature - Aws::ElasticsearchService - Added support for Amazon Elasticsearch Service
  (Amazon ES), is a managed service that makes it easy to deploy, operate, and scale
  Elasticsearch in the AWS cloud. Elasticsearch is a popular open-source search and
  analytics engine for use cases such as log analytics, real-time application
  monitoring, and click stream analytics. With Amazon ES, you get direct access
  to the Elasticsearch open-source API so that existing code and applications will
  work seamlessly. You can set up and configure your Amazon ES cluster in minutes
  from the AWS Management Console. Amazon Elasticsearch Service provisions all the
  resources for your cluster and launches it. Amazon ES also automatically detects
  and replaces failed Elasticsearch nodes, reducing the overhead associated with
  self-managed infrastructures. You can easily scale your cluster with a single
  API call or a few clicks in the AWS Management Console.

2.1.25 (2015-10-01)
------------------

* Feature - Aws::RDS - Added support for CopyTagsToSnapshot.

  See [related GitHub issue #887](https://github.com/aws/aws-sdk-ruby/pull/887).

* Feature - Aws::CloudTrail - This release of CloudTrail includes support for log
  file integrity validation, log encryption with AWS KMS-Managed Keys (SSE-KMS),
  and trail tagging.

2.1.24 (2015-09-29)
------------------

* Feature - Aws::SES - Amazon SES can now accept incoming emails. You can
  configure Amazon SES to deliver messages to an Amazon S3 bucket, call an AWS
  Lambda function, publish notifications to Amazon SNS, drop messages, or bounce
  messages.

* Feature - Aws::EC2 - Add `#modify_spot_fleet_request` API to Amazon EC2 client
  and bump API version to 2015-10-01.

* Feature - Aws::CloudFormation - Add `#describe_account_limits` API to allow
  users to view account-level limits imposed by AWS CloudFormation. Also adds
  optional parameter `:resource_types` to `#create_stack` and `#update_stack`
  APIs.

2.1.23 (2015-09-17)
------------------

* Upgrading - Aws::S3::Client - Deprecated two methods:

  * `Aws::S3::Client#put_bucket_lifecycle`
  * `Aws::S3::Client#get_bucket_lifecycle`

  These methods have been replaced by:

  * `Aws::S3::Client#put_bucket_lifecycle_configuration`
  * `Aws::S3::Client#get_bucket_lifecycle_configuration`

  Amazon S3 now permits multiple transitions for a single
  lifecycle rule. Previously, a lifecycle rule would
  return a transition value, or `nil`. Now it will always
  return an array.

* Feature - Aws::S3 - Added support for the new STANDARD_IA storage class,
  lifecycle transitions and cross region replication with storage class
  features.

2.1.21 (2015-09-15)
------------------

* Feature - Aws::EC2 - Added two new fields to expose data encryption key id,
  and state message.

* Feature - Aws::EC2 - Added support for the new "Diversified" allocation
  strategy.

* Feature - Aws::ElasticFileSystem - Added support for optional mount
  target ID to the DescribeMountTargets operation.

* Feature - Aws::Route53 - Added support for calculated health checks and
  latency health checks.

* Issue - Aws::S3 - Resolved an issue which could raise a signature error
  when making reqeusts against an object where the key is prefixed
  with the bucket name. This only affects classic S3 signatures, not requests
  using signature version 4.

  See [related GitHub issue #923](https://github.com/aws/aws-sdk-ruby/issues/923).

2.1.20 (2015-09-10)
------------------

* Feature - Aws::IAM - Added two new APIs that enable programmatic access to the
  AWS Identity and Access Management policy simulator.

* Feature - Aws::S3::Object - Added a `#move_to` method that copies
  the object to the destination and then deletes the source object.

* Issue - Aws::S3::Client - Added support for errors returned from
  Amazon S3 in the body of HTTP 200 responses. Previously only
  the `#complete_multipart_upload` operation handled these errors.
  Extended this to `#copy_object` and `#upload_part_copy`.

* Issue - Aws::S3::Object - The `#presigned_post` method now respects
  the configured custom endpoint.

  ```ruby
  obj = Aws::S3::Object.new("bucket", "key", endpoint: "http://custom-endpoint.com")
  obj.presigned_post.url
  #=> "http://bucket.custom-endpoint.com"
  ```

* Issue - Aws::S3::Presigner - Added validation to `#presigned_url` to
  ensure the `:key` option is not an empty string. This prevents a
  presigned `:get_object` URL from becoming a presigned `:list_objects`
  request on the bucket.

2.1.19 (2015-09-03)
------------------

* Feature - Aws::StorageGateway - Adds tagging support for AWS Storage Gateway.

* Issue - Aws::InstanceProfileCredentials - Expanded retry logic in the
  `Aws::InstanceProfileCredentials` class. Previously this class could
  raise JSON parse error if the instance metadata service responded but
  with an invalid JSON document. These errors will be retried 3 times now
  with backoff.

  See [related GitHub issue #913](https://github.com/aws/aws-sdk-ruby/issues/913).

* Feature - Aws::EC2 - Implemented `Aws::EC2::VpcPeeringConnection#exists?`.

  See [related GitHub issue #916](https://github.com/aws/aws-sdk-ruby/issues/916).

* Issue - Aws::EC2 - Added missing pagination configuraiton for the
  `Aws::EC2::Client#describe_vpc_peering_connections` operation. Without
  this, calling `#each` on the response fails to trigger paging and
  raises an error.

  See [related GitHub issue #914](https://github.com/aws/aws-sdk-ruby/issues/914).

2.1.18 (2015-08-31)
------------------

* Feature - Aws::EC2 - Launch Amazon EC2 Spot instances that have the lowest
  price per unit of capacity using the updated RequestSpotFleet API. Define and
  set target capacity in any unit including instances, vCPUs, memory, storage,
  or network throughput. You can specify how much each instance type is worth to
  your application, and automatically adjust your bid price for each type. This
  enables you to bid on multiple instance types in a single request and
  provision cost-effective capacity, regardless of instance type.

* Feature - Aws::ElasticLoadBalancing - Added a waiter that will poll
  until an instance has been deregistered.

  See [related GitHub issue #793](https://github.com/aws/aws-sdk-ruby/issues/793).

* Issue - Aws::EC2 - Added a missing `#load` method for
  `Aws::EC2::NetworkInterfaceAssociation`.

  See [related GitHub issue #890](https://github.com/aws/aws-sdk-ruby/issues/890).

* Issue - Retries - No longer retrying non-networking errors generated during
  HTTP handling.

  See [related GitHub issue #912](https://github.com/aws/aws-sdk-ruby/issues/912).

2.1.17 (2015-08-27)
------------------

* Feature - Aws::ConfigService - Adds support for the
  `#list_discovered_resources` API.

2.1.16 (2015-08-25)
------------------

* Feature - Aws::CodePipeline - Allows specification of an encryption key to use
  with the artifact bucket when creating and updating a pipeline.

* Feature - Logging - Now filtering potentially sensitive request parameters
  from logs. There is a default list of sensitive request parameters that
  should be filtered:

  ```ruby
  require 'logger'

  kms = Aws::KMS::Client.new(logger: Logger.new($stdout))
  kms.encrypt({
    key_id: "key-arn",
    plaintext: "secret-data",
  })
  ```

  Produces:

  ```
  I, [2015-08-24T14:53:04.545354 #47906]  INFO -- : [Aws::KMS::Client 400 0.362187 0 retries] encrypt({key_id:"key-arn",plaintext:"[FILTERED]"})
  ```

  You can pass your own list of params to filter when you construct an
  `Aws::Log::Formatter` with a custom pattern.

  See [related GitHub issue #908](https://github.com/aws/aws-sdk-ruby/issues/908).

* Issue - Aws::PageableResponse::UnsafeEnumerableMethods - `respond_to?`
  was not being forwarded to ancestors and it was always retuning `false`
  except for #count.

2.1.15 (2015-08-20)
------------------

* Issue - Aws::Glacier - Resolved an issue with how dates were serialized
  when specifying start and end dates with an inventory retrieval job.

* Feature - Aws::S3 - Added support for event notification filter rules.

2.1.14 (2015-08-12)
------------------

* Feature - Aws::ElasticBeanstalk - Added support for the two new
  operations, DescribeEnvironmentHealth, and DescribeInstanceHealth.

* Feature - Managed Multipart Copies - Added support to `Aws::S3::Object`
  for managed multipart copies of large objects.

      obj = Aws::S3::Object.new('bucket', 'key')

      obj.copy_from('source-bucket/source-key', multipart_copy: true)
      obj.copy_to('target-bucket/target-key', multipart_copy: true)

* Feature - Aws::S3::Object - Improved the interfaces for copying objects.
  There are three new supported signatures for calling `#copy_from`.
  Also added the logical pair, `#copy_to`.

      obj = Aws::S3::Object.new('bucket', 'key')

      obj.copy_from('source-bucket/source-key')
      obj.copy_to('target-bucket/target-key')

      obj.copy_from(bucket: 'source-bucket', key: 'source-key')
      obj.copy_to(bucket: 'target-bucket', key: 'target-key')

      other_obj = Aws::S3::Object('other-bucket', 'other-key')
      obj.copy_from(other_obj)
      obj.copy_to(other_obj)

  For backwards compatibility, the old form of `#copy_from` still works.

      obj.copy_from(copy_source: 'source-bucket/source-key')

* Issue - Resources - Resolved an issue with resource actions that source
  request parameters from data members.

  See [related GitHub issue #893](https://github.com/aws/aws-sdk-ruby/issues/893).

2.1.13 (2015-08-06)
------------------

* Feature - Aws::SWF - You can now use Amazon Simple Workflow Service to trigger
  AWS Lambda functions.

2.1.12 (2015-08-04)
------------------

* Feature - Aws::DeviceFarm - Added support for testing iOS applications with
  AWS Device Farm. You can now upload your iOS apps and test them on real Apple
  phones and tablets in the AWS Cloud. You can select from a built-in fuzz test
  or upload your own tests written with Appium Java JUnit, Appium Java TestNG,
  Calabash, UI Automation, or XCTest. Results in AWS Device Farm are consistent
  regardless of the application type (Android, Fire OS, iOS) you upload and test
  framework you select.

* Issue - Aws::EC2 - Updated two `Aws::EC2::Client#wait_until` waiters to
  retry instance ID not found errors. The two waiters are:

  * `:instance_running`
  * `:instance_status_ok`

  See [related GitHub issue #859](https://github.com/aws/aws-sdk-ruby/issues/859).

2.1.11 (2015-07-30)
------------------

* Feature - Aws::RDS - Added support for cluster operations for Aurora.

* Feature - Aws::OpsWorks - Added support for ECS clusters to `Aws::OpsWorks::Client`.

* Feature - Virtual Hosted Bucket Urls - You can now generated URLs and pre-signed URLs
  from `Aws::S3` using a CNAME for your virtually hosted buckets.

      bucket = Aws::S3::Bucket.new('my.bucket.com')

      bucket.url
      #=> 'https://s3.amazonaws.com/my.bucket.com'

      bucket.url(virtual_host: true)
      #=> 'http://my.bucket.com'

      bucket.object('key').public_url
      #=> 'https://s3.amazonaws.com/my.bucket.com/key'

      bucket.object('key').public_url(virtual_host: true)
      #=> 'http://my.bucket.com/key'

      bucket.object('key').presigned_url(:get)
      #=> 'https://s3.amazonaws.com/my.bucket.com/key?...'

      bucket.object('key').presigned_url(:get, virtual_host: true)
      #=> 'http://my.bucket.com/key?...'

  See [related GitHub issue #786](https://github.com/aws/aws-sdk-ruby/issues/786).

* Feature - Signature Version 4 - Added support for signing requests against
  hosts that do not use the standard port for the given HTTP scheme. This
  makes it possible to use the signer against test endpoints.

  See [related GitHub issue #883](https://github.com/aws/aws-sdk-ruby/issues/883).

2.1.10 (2015-07-29)
------------------

* Issue - Aws::CognitoIdentity - Six operations of `Aws::CognitoIdentity::Client`
  were whitelisted to be unsigned when sent in version 2.1.9. Two of these
  operations were incorrectly documented in the API reference and require
  authentication. Fixed this and added integration tests to ensure they are
  correctly signed.

  See [related GitHub issue #882](https://github.com/aws/aws-sdk-ruby/issues/882).

2.1.9 (2015-07-28)
------------------

* Feature - Aws::CloudWatchLogs - Added support for destinations.  You can now
  use destinations to subscribe to cross-account real-time feed of log events
  from CloudWatch Logs and have it delivered to an Amazon Kinesis stream for
  custom processing, analysis, or loading to other systems.

* Feature - Aws::S3 - Added support for the new long tail storage class and the new
  storage class headers in the GET and HEAD Object responses.

* Feature - Aws::S3 - Added support for the new object delete notification events.

* Issue - Aws::Glacier - `Aws::Glacier::Account#vaults` was not populating the account
  in the call to list vaults.

* Issue - Aws::EC2 - Resolved a regression with serializing `Aws::Structure` types in
  `Aws::EC2::Client` requests. The `Aws::Query::EC2ParamBuilder` class is responsible
  for testing protocol specific implementations but it tests with input that is loaded
  from a JSON document into a vanilla Ruby hash which does not trigger this behavior.

  The other protocol builder classes have full unit test suites, while the EC2 param
  builder did not. A test case has been added to cover this regression.

  See [related GitHub issue #881](https://github.com/aws/aws-sdk-ruby/issues/881).

* Feature - KMS Client-Side-Encryption - Added support to `Aws::S3::Encryption::Client` for using AWS Key Management Service (KMS) to manage master encryption keys.

  ```ruby
  s3_enc = Aws::S3::Encryption::Client.new(kms_key_id: 'kms-key-id')
  s3_enc.put_object(bucket:'aws-sdk', key:'secret', body:'data')
  s3_enc.get_object(bucket:'aws-sdk', key:'secret').body.read
  #=> 'data'
  ```

  This implementation is compatible with objects written by the AWS SDK for Java
  encryption client.

2.1.8 (2015-07-23)
------------------

* Feature - Aws::EC2 - Added support for new spot fleet launch specification features.

* Feature - Aws::Glacier - Added support for vault locks.

* Feature - Aws::ElasticMapReduce - Adds support for Amazon EMR release 4.0.0, which
  includes a new application installation and configuration experience, upgraded
  versions of Hadoop, Hive, and Spark, and now uses open source standards for ports
  and paths. To specify an Amazon EMR release, use the release label parameter
  (AMI versions 3.x and 2.x can still be specified with the AMI version parameter).

  For more information on Amazon EMR release 4.0.0, please visit:
  https://docs.aws.amazon.com/ElasticMapReduce/latest/ReleaseGuide/emr-release-components.html

* Issue - Aws::IAM - Added missing paginator for `Aws::IAM::Client#list_policy_versions`.

  See [related GitHub issue #879](https://github.com/aws/aws-sdk-ruby/issues/879).

* Feature - Waiters - Added two new waiters:

  * `:instance_profile_exists` - `Aws::IAM::Client`
  * `:app_exists` - `Aws::OpsWorks::Client`

  See [related GitHub pull request #866](https://github.com/aws/aws-sdk-ruby/pull/866)

* Issue - Stubbing - Added missing support for stubbing query protocol errors by their
  error code.

  See [related GitHub pull request #873](https://github.com/aws/aws-sdk-ruby/pull/873)

* Feature - Aws::CognitoIdentity - The following operations can now be called from
  Aws::CognitoIdentity::Client without credentials:

  * `#get_credentialsForIdentity`
  * `#get_id`
  * `#get_open_id_token`
  * `#list_identity_pools`
  * `#unlink_developer_identity`
  * `#unlink_identity`

  See [related GitHub pull request #862](https://github.com/aws/aws-sdk-ruby/pull/862)

2.1.7 (2015-07-14)
------------------

* Issue - Aws::Json - Resolved an issue with dumping JSON documents from the
  standard library JSON implementation.

2.1.5 (2015-07-14)
------------------

* Feature - Aws::DeviceFarm - Added support for AWS Device Farm.

* Feature - Aws::DynamoDBStreams - Added support for Amazon DynamoDB Streams.

* Feature - Aws::DynamoDB - Added support for consistent reads with the
  Scan API.

2.1.4 (2015-07-09)
------------------

* Feature - Aws::ECS - Amazon EC2 Container Service now supports the UDP
  protocol.

* Feature - Aws::IAM - Adds support for SSH Public Key Operations.

* Feature - Aws::SES - Adds support for cross-account sending, which is
  available through the use of the sending authorization feature.

* Feature - Aws::CodeCommit - Adds support for AWS CodeCommit.

* Feature - Aws::CodePipeline - Adds support for AWS CodePipeline.

2.1.3 (2015-07-07)
------------------

* Feature - Aws::AutoScaling - Adds support for step policies. Step scaling
  policies allow customers to scale their Auto Scaling groups based on the
  magnitude of the alarm breach, giving them more control over how Auto Scaling
  responds to an alarm breach and enables them to scale their groups faster.

* Feature - Aws::EC2 - Amazon Elastic Block Store snapshot copy support for
  customer-managed encryption keys.

* Feature - Aws::EC2 - Added a `Aws::EC2::Route` resource class with the
  associated `#delete` and `#replace` operations.

* Issue - Aws::DynamoDB - The Aws::DynamoDB::Client#stub_data method will
  now observe the configured `:simple_attributes` option and conditionally
  accept and return simple attribute values.

  See [related GitHub pull request #863](https://github.com/aws/aws-sdk-ruby/pull/863)

* Issue - Resource - Fixed behavior of `#wait_until` option `max_attempts: nil`
  to match documentation and allow unlimited retries. Default number of retries
  remains at 10.

  See [related GitHub pull request #855](https://github.com/aws/aws-sdk-ruby/pull/855)

2.1.2 (2015-06-24)
------------------

* Feature - Aws::CloudFront - Can now set `:max_ttl` and `:default_ttl` within
  `:cache_behaviors`. New API version `2015-04-17`.

* Feature - Aws::Lambda - Adds `java8` as a supported runtime.

* Feature - Aws::CognitoSync - Add `ConcurrentModificationException` error.

* Feature - Aws::CognitoIdentity - Add `ConcurrentModificationException` error.

* Feature - Aws::ConfigService - You can now set up AWS Config to record changes
  for specific resource types.

* Feature - Aws::OpsWorks - You can specify which agent version will be used in
  your AWS OpsWorks managed instances.

* Feature - Aws::Glacier - Amazon Glacier now allows you to tag your Amazon
  Glacier vaults for easier resource and cost management. Tags are labels that
  you can define and associate with your vaults, and using tags adds filtering
  capabilities to operations such as AWS cost reports.

* Feature - Aws::Redshift - You can create automatic cross-region backups of
  your KMS encrypted clusters for disaster recovery.

2.1.1 (2015-06-18)
------------------

* Feature - Aws::ECS - Added support for environment variable overrides.

* Feature - XML Parsing - Added support for the new Oga XML parser. This
  is enabled by default when the gem is present. You can force this
  by setting the XML parsing engine:

      Aws::Xml::Parser.engine = :oga

* Issue - Aws::DynamoDB - Resolved an issue with pageable responses where the
  paging token values contained attribute values.

  See [related GitHub issue #843](https://github.com/aws/aws-sdk-ruby/issues/843)

* Issue - Aws::IAM - Added missing paginator configurations for the newer
  IAM attached policy operations.

  See [related GitHub issue #841](https://github.com/aws/aws-sdk-ruby/issues/841)

* Issue - PageableResponse - Resolved an issue where Enumerable was not
  correctly getting mixed into pageable responses.

  See [related GitHub issue #842](https://github.com/aws/aws-sdk-ruby/issues/842)

* Issue - Shared Credentials - Resolved an issue where the shared credentials
  provider would fail to parse profiles which contained an inline comment.

  See [related GitHub issue #837](https://github.com/aws/aws-sdk-ruby/issues/837)

* Feature - Aws::IAM - Added `Aws::IAM::User#exists?`.

  See [related GitHub issue #840](https://github.com/aws/aws-sdk-ruby/issues/840)

* Feature - REPL - Added a short-cut inside the `aws.rb` REPL
  for accessing a resource from a client.

  ```ruby
  Aws> ec2
  #=> #<Aws::EC2::Client>
  Aws> ec2.resource
  #=> #<Aws::EC2::Resource>
  ```

2.1.0 (2015-06-11)
------------------

* Feature - Aws::EC2 - Adds support for Amazon VPC flow logs.

* Feature - Aws::ECS - Adds the `#update_container_agent` method, as well
  as paginator and waiter support.

* Feature - Aws::AutoScaling - Adds the `#describe_load_balancers` method,
  and adding the `:load_balancer_names` parameter to the
  `#update_auto_scaling_group` method.

* Feature - Fewer gem dependencies - Removed the dependency on the follow
  two 3rd party gems:

  * `multi_json`
  * `builder`

  For JSON parsing and dumping, the SDK will attempt to use the `Oj`
  gem if available, and will fall back on `JSON` from the Ruby
  standard library.

  The only remaining gem dependency is `jmespath`.

* Feature - Service Types - Added struct classes for all AWS data types.
  Previous versions used anonymous structure classes for response data.

      Aws::IAM::Types.constants
      #=> [:AccessKey, :AccessKeyLastUsed, :AccessKeyMetadata, ...]

  Each of these data type classes are now fully documented in the
  [api reference docs](http://docs.aws.amazon.com/sdkforruby/api/index.html).

* Feature - Examples - The API reference documentation can now load client
  operation examples from disk. This allows users to contribute examples
  to the documentation. It also allows for more targeted examples
  than the auto-generated ones previously used.

  Examples are stored inside the examples folder at the root of this
  repository. https://github.com/aws/aws-sdk-ruby/tree/master/examples

* Feature - Documentation - Significant work has gone into API reference
  documentation. The changes made are based on user feedback. Some of
  the many changes include:

  * More concise syntax examples
  * Response structure examples
  * Request and response data structures each documented as their own type
  * Expanded resource interface documentation on inputs and return values

  Expect more documentation improvements.

* Feature - Smaller Gem - Reduced the size of the `aws-sdk-core` gem
  by removing the AWS API documentation source files from the gemspec.
  This reduces the total size of the gem by half.

* Feature - Stub Data - Added a `#stub_data` method to `Aws::Client` that
  makes it trivial to generate response data structures.

      s3 = Aws::S3::Client.new

      s3.stub_data(:list_buckets)
      #=> #<struct Aws::S3::Types::ListBucketsOutput buckets=[], owner=#<struct Aws::S3::Types::Owner display_name="DisplayName", id="ID">>

  You can also provide an optional hash of data to apply to the stub.
  The data hash will be validated to ensure it is properly formed and
  then it is applied. This makes it easy to generated nested response
  data.

      s3.stub_data(:list_buckets, buckets:[{name:'aws-sdk'}])
      #=> #<struct Aws::S3::Types::ListBucketsOutput
        buckets=[
          #<struct Aws::S3::Types::Bucket name="aws-sdk", creation_date=nil>
        ],
        owner=#<struct Aws::S3::Types::Owner display_name="DisplayName", id="ID">>

* Feature - Shared Response Stubs - You can now provide default stubs to a
  client via the constructor and via `Aws.config`. This can be very useful
  if you need to stub client responses, but you are not constructing the
  client.

      Aws.config[:s3] = {
        stub_responses: {
          list_buckets: { buckets: [{name:'aws-sdk'}]}
        }
      }

      s3 = Aws::S3::Client.new
      s3.list_buckets.buckets.map(&:name)
      #=> ['aws-sdk']

  See [related GitHub issue aws/aws-sdk-core#187](https://github.com/aws/aws-sdk-core-ruby/issues/187)

* Issue - Response Stubbing - When using response stubbing, pagination
  tokens will no longer be generated. This prevents stubbed responses
  from appearing pageable when they are not.

  See [related GitHub issue #804](https://github.com/aws/aws-sdk-ruby/pull/804)

* Issue - Aws::DynamoDB::Client - Resolved an issue where it was not
  possible to stub attribute values. You can now stub DynamoDB responses
  as expected:

      ddb = Aws::DynamoDB::Client.new(stub_responses:true)
      ddb.stub_responses(:get_item, item: { 'id' => 'value', 'color' => 'red' })
      ddb.get_item(table_name:'table', key:{'id' => 'value'})
      #=> #<struct Aws::DynamoDB::Types::GetItemOutput item={"id"=>"value", "color"=>"red"}, consumed_capacity=nil>

  See [related GitHub issue #770](https://github.com/aws/aws-sdk-ruby/pull/770)

* Feature - SSL Peer Verification - Added a method to configure a default
  SSL CA certificate bundle to be used when verifying SSL peer certificates.
  V1 did this by default, v2 is now opt-in only.

      Aws.use_bundled_cert!

  This method can be very useful for Ruby users on Windows where OpenSSL
  does not tend to have access to a valid SSL cert bundle.

  See [related GitHub issue aws/aws-sdk-core#166](https://github.com/aws/aws-sdk-core-ruby/issues/166)

* Feature - Eager auto-loading - Added a utility method that eagerly loads
  classes and modules. This can help avoid thread-safety issues with
  autoload affecting some versions Ruby.

      # autoload specific services (faster)
      Aws.eager_autoload!(services: %w(S3 EC2))

      # autoload everything
      Aws.eager_autoload!

  See [related GitHub issue #833](https://github.com/aws/aws-sdk-ruby/pull/833)

* Issue - Response Stubbing - Clients with `stub_responses: true` were still
  attempting to load credentials from ENV, and the EC2 instance metadata
  service. Instead, stubbed clients will now construct fake static credentials.

  See [related GitHub issue #835](https://github.com/aws/aws-sdk-ruby/pull/835)

* Upgrading - Pageable Responses - Due to frequent confusion caused by
  having every response be decorated as pageable, only operations that can
  possibly have multiple responses are now decorated.

  This ensures that if you call `#each` on a non-pageable response that
  an appropriate `NoMethodError` is raised instead of doing nothing. Simply
  remove calls to `#each` if you encounter this unlikely situation.

      s3 = Aws::S3::Client.new
      s3.head_object(bucket:'name', key:'key').each do |page|
      end
      #=> raises NoMethodError for #each, HEAD object is not pageable

  This is correctly reflected now in the API reference documentation as
  well.

* Upgrading - Seahorse::Model - The internal representation of AWS API models
  has been updated. Users digging into internals of the API model will need
  to update their code to deal with shape references.

  **This change should not affect users of the public SDK interfaces**.

  Complex shapes, structures, lists, and maps, now use shape references
  to nest other shapes. The entire API model now loaded when the service client
  is defined. This eliminates the need to maintain a complex map of all
  shapes and define them lazily. This has allows for massive simplifications
  around API loading, especially when dealing with thread-safety concerns.

2.0.48 (2015-06-04)
------------------

* Feature - Aws::CloudWatchLogs - Adds support for the `#put_subscription_filter`,
  `#describe_subscription_filters`, and `#delete_subscription_filter` API
  operations.

* Feature - Aws::CognitoIdentity - Adds support for the `#delete_identities`
  API operation, and hiding disabled identities with the `#list_identities` API
  operation.

* Feature - Aws::StorageGateway - Updated Aws::StorageGateway API to add support
  for the `#list_volume_initiators` API operation.

* Issue - Aws::DynamoDB - Resolve an issue where IO objects were not properly
  marshaled, leading to runtime errors.

  See [related GitHub issue #831](https://github.com/aws/aws-sdk-ruby/issues/831)

* Issue - Aws::S3 - Resolved an issue with the URL decoding of keys in the
  response to a call to `Aws::S3::Client#list_objects`.

  See [related GitHub issue #828](https://github.com/aws/aws-sdk-ruby/pull/828)

2.0.47 (2015-05-28)
------------------

* Feature - Aws::Lambda - Lambda now supports specifying an Amazon S3 object as
  the function zip when creating and updating Lambda functions.

2.0.46 (2015-05-26)
------------------

* Feature - Aws::EFS - Amazon Elastic File System (Amazon EFS) is a file storage
  service for Amazon Elastic Compute Cloud (Amazon EC2) instances. Amazon EFS is
  easy to use and provides a simple interface that allows you to create and
  configure file systems quickly and easily. With Amazon EFS, storage capacity
  is elastic, growing and shrinking automatically as you add and remove files,
  so your applications have the storage they need, when they need it.

2.0.45 (2015-05-21)
------------------

* Feature - Aws::ElasticTranscoder - Elastic Transcoder now supports additional
  formats, including MXF, FLAC, and OGA, and additional flexibility for your
  output audio. You can use these formats to transcode files to the XDCAM format
  or to a lossless audio format.

* Feature - Aws::Kinesis - The Amazon Kinesis API `#get_records` now includes a
  new parameter `:millis_behind_latest`: the number of milliseconds the
  GetRecords response is from the end of the stream, indicating how far behind
  real time a consumer is.

* Feature - Aws::KMS - Adds the `#update_alias` API, which allows you to update
  the association of a key alias from one key to another within a region.

* Issue - Aws:SQS - Resolved an issue with the SQS queue poller and changing message
  visibility timeouts.

  See [related GitHub issue #821](https://github.com/aws/aws-sdk-ruby/pull/821)

2.0.44 (2015-05-18)
------------------

* Issue - Aws::EC2 - Two waiters were added in v2.0.39 and then lost in v2.0.40
  in this commit: https://github.com/aws/aws-sdk-ruby/commit/f0fad0a038a4f81b0a697f7c367fd2735d9dd698.
  The waiters did not get merged upstream and got squashed in the automated release.
  Reverting this loss. Actual changes have also been merged upstream now.

  See [related GitHub issue #818](https://github.com/aws/aws-sdk-ruby/pull/818)

* Feature - Aws::ElasticBeanstalk - Added resource names to
  Aws::ElasticBeanstalk::Client configuration option settings.

* Feature - Aws::OpsWorks - Added support for custom auto-scaling based on
  CloudWatch alarms.

* Feature - Aws::EC2 - Added support for the new Spot Fleet API operations.

* Issue - Aws::S3 - Resolved a regression with presigned S3 urls where input parameters
  that are normally sent as x-amz-* headers had to be serialized onto the
  GET or PUT request. This restores the behavior where they are hoisted
  onto the request uri as query string parameters.

  See [related GitHub issue #816](https://github.com/aws/aws-sdk-ruby/pull/816)

2.0.43 (2015-05-14)
------------------

* Feature - Aws::DirectoryService - APIs are now supported for the Simple AD and
  AD Connector directories in AWS Directory Service, enabling you to
  programmatically create and configure these directories via the AWS SDK for
  Ruby.

* Feature - Aws::EC2 - Added support for Amazon VPC Endpoints for Amazon S3.

* Feature - Aws::EC2 - Added support to migrate an Elastic IP Address from
  EC2-Classic to EC2-VPC.

* Feature - Aws::Route53Domains - `#check_domain_availability` can now return
  `DONT_KNOW` when a definitive response has not been received from the
  registry, such as during domain registry scheduled maintenance periods.
  UnsupportedTLD is now a potential error from more Route 53 Domains activities.

* Feature - Aws::CloudWatchLogs - Added support for the `#filter_log_events` API
  for searching logs within a group.

* Feature - Aws::CloudFormation - Added new parameters to the return type of
  the `#get_template_summary` API.

2.0.42 (2015-05-07)
------------------

* Feature - Aws::Glacier - Added support for vault access policies.

* Issue - Aws::IAM - Added the missing pagination configuration for
  `Aws::IAM::Client#list_policies`.

  See [related GitHub issue #805](https://github.com/aws/aws-sdk-ruby/pull/805)

* Issue - HTTP Handler - Applied a local fix for a Net::HTTP bug. Net::HTTP
  will retry failed requests when the request method is idempotent, such as
  HTTP GET, PUT, etc. Unfortunately, Net::HTTP will create a new request, but
  yield bytes to the same callback after bytes have already been yielded. This
  causes the response data sink to contain a truncated response, followed
  by a full response.  This results in correct data.

  The fix current is to disable idempotent retries until this is fixed upstream.

  See [related GitHub issue #799](https://github.com/aws/aws-sdk-ruby/pull/799)

* Feature - Aws::S3::Encryption::Client - You can now pass vanilla
  client constructor options to the encryption client constructor and these
  will pass through when generating the underlying client. This increases
  the parity with the vanilla client.

  See [related GitHub issue #800](https://github.com/aws/aws-sdk-ruby/pull/800)
  and [related GitHub issue #801](https://github.com/aws/aws-sdk-ruby/pull/801).

* Feature - Aws::S3 - Added the ability to pass a block to
  `Aws::S3::Encryption::Client#get_object` that yields data as it is
  decrypted. This increases parity with the vanilla `Aws::S3::Client#get_object`
  method.

  See [related GitHub issue #794](https://github.com/aws/aws-sdk-ruby/pull/794).

* Issue - Aws::S3 - Resolved an issue were the bucket exists waiter could raise
  when receiving a 301 permanent redirect from Amazon S3. This happens when
  the HEAD request is made against a region different from where the bucket
  exists. Instead this will now cause the `:bucket_exists` waiter to return a
  truthy value and not raise.

  See [related GitHub issue #796](https://github.com/aws/aws-sdk-ruby/pull/796).

* Issue - Aws::EC2 - The `:volume_deleted` waiter no longer raises an error on a
  400 `Aws::EC2::Errors::InvalidVolumeNotFound` error. Instead it returns
  with success.

  See [related GitHub issue #789](https://github.com/aws/aws-sdk-ruby/pull/789).

* Feature - Aws::S3 - Added a client-side check to ensure HTTP response bodies are
  not truncated before all of the expected bytes have been received. This check is
  performed for all non-HEAD requests where a 'Content-Length' response header is
  present.

  When fewer bytes are returned, then the request is retried up to the configured
  `:retry_limit` number limit of attempts. This limit defaults to 3.


2.0.41 (2015-04-27)
------------------

* Feature - Aws::DynamoDB - Added support to `Aws::DynamoDB::Client` for
  key conditions.

2.0.40 (2015-04-23)
------------------

* Feature - Aws::EC2 - Added support for the new 2015-03-01 API version.

* Feature - Aws::IAM - Added support for `Aws::IAM::Client#access_key_last_used`.

* Feature - Aws::CloudSearchDomain - Added support for response expressions.

2.0.39 (2015-04-16)
------------------

* Feature - Aws::ConfigService - SNS topics on AWS Config's DeliveryChannel are
  now optional. If the SNS delivery is turned off the last status will be
  Not_Applicable.

* Feature - Aws::CognitoSync - Adds support for Amazon Cognito Events, a new
  feature that allows developers to run an AWS Lambda function in response to
  important events in Amazon Cognito. The first event supported is the Sync
  Trigger event.

* Feature - Waiters - Added waiters for:

  * Aws::CloudFormation
  * Aws::ElastiCache
  * Aws::OpsWorks

  Expanded waiters for:

  * Aws::EC2

  Also resolved a few issues in the Aws::CloudFront and Aws::RDS waiters.

  See [related GitHub issue #758](https://github.com/aws/aws-sdk-ruby/pull/758).

* Feature - Aws::DynamoDB - Added resource class for `Aws::DynamoDB::Table`.

* Issue - XML Parsing - Resolved an issue where XML string values were not
  being unescaped.

  See [related GitHub issue #780](https://github.com/aws/aws-sdk-ruby/issues/780).

* Issue - Resources - Resolved an issue that prevented the `aws-sdk-resources`
  gem from loading resource definitions that have naming conflicts with
  the Capistrano project.

  The `#safe_define_method` is intenteded to ensure the resource methods do
  not conflict with methods of Object, such as `#send`, and `#object_id`.
  These are now tested only in local testing but not at runtime. This allows
  for projects to add functionality to Object without triggering this
  check.

  See [related GitHub issue #771](https://github.com/aws/aws-sdk-ruby/issues/771).

2.0.38 (2015-04-09)
------------------

* Upgrading - Aws::Lambda - AWS Lambda is exiting their preview period and has
  made changes to their control plane APIs. If you are invoking methods outside
  of `Aws::Lambda::Client#invoke_async`, you should switch to the
  `Aws::LambdaPreview::Client` until you can upgrade.

  ```ruby
  # returns the stable 2015-03-01 API client
  lambda = Aws::Lambda::Client.new

  # returns the preview API client
  lambda = Aws::LambdaPreview::Client.new
  ```

* Feature - Aws::WorkSpaces - Added support for Amazon WorkSpaces.

* Feature - Aws::ECS - You can now use the Amazon ECS Service scheduler to
  manage long-running applications and services. The Service scheduler allows
  you to distribute traffic across your containers using Elastic Load Balancing.

* Feature - Aws::S3 - Added support for resource based permissions for invoking
  Lambda functions via bucket notifications.

* Upgrading - Aws::S3::Client - Deprecated two methods:

  * `Aws::S3::Client#put_bucket_notification`
  * `Aws::S3::Client#get_bucket_notification`

  These methods have been replaced by:

  * `Aws::S3::Client#put_bucket_notification_configuration`
  * `Aws::S3::Client#get_bucket_notification_configuration`

  The method signatures for the old two methods did not correctly allow users
  to specify multiple topic, queue, and lambda function configurations. The
  new methods, suffixed by `_notification` correct these errors. The new
  methods now also support resource based permissions on notifications to
  lambda functions.


  ```ruby
  s3 = Aws::S3::Client.new

  # old
  s3.put_bucket_notification(
    bucket: 'aws-sdk',
    notification_configuration: {
      topic_configuration: { id:'id1', events:[...] },
      queue_configuration: { id:'id2', events:[...] },
      cloud_function_configuration: { id:'id3', events:[...], invocation_role: '...' },
    }
  )

  # new
  s3.put_bucket_notification_configuration(
    bucket: 'aws-sdk',
    notification_configuration: {
      topic_configurations: [
      	{ id:'id1', events:[...] }
      ],
      queue_configurations: [
      	{ id:'id2', events:[...] }
      ],
      lambda_function_configurations: [
      	{ id:'id3', events:[...] }
      ]
    }
  )
  ```

  Notice the `:lambda_function_configurations` does not take an
  `:invocation_role` and that each of the configuration types now takes
  a list of configurations.

* Feature - Aws::MachineLearning - Added support for Amazon Machine Learning.

2.0.37 (2015-04-07)
------------------

* Feature - Aws::DataPipeline - This release introduces support for AWS Data
  Pipeline Deactivate feature. You now have the ability to deactivate a running
  pipeline and activate it later at a time of your choosing.

* Feature - Aws::ElasticBeanstalk - Added new `#abort_environment_update`
  operation for Elastic Beanstalk. Added a new parameter, `:solution_stack_name`,
  to the `#update_environment` operation for ElasticBeanstalk.

* Issue - Aws::IAM - Resolved an issue with `Aws::IAM::Resource#policy` that would
  raise an `ArgumentError`. The resource definition incorrectly supplied the
  argument as the "PolicyArn" when it should have been simply "Arn".

  See [related GitHub issue #768](https://github.com/aws/aws-sdk-ruby/issues/768).

2.0.36 (2015-04-06)
------------------

* Issue - Gemspec - Corrected the paths in the gemspec for the API files.

2.0.35 (2015-04-06)
------------------

* Issue - Xml Parser - Resolved an issue with the recent XML parser update that
  caused bugs when using Nokogiri. Nokogiri will inconsistently trigger one or
  multiple text events for XML elements. This seems to be affected by the total
  size of the XML document.

  The XML parser now supports SAX parsing engines that trigger mulitple text
  events for a single XML element value. Added test where a dummy engine simulates
  this behavior.

  See [related GitHub issue #764](https://github.com/aws/aws-sdk-ruby/issues/764).

* Issue - Response Stubbing - Resolved an issue that prevented response stubs from
  forcing a nil value for scalars.

  See [related GitHub issue #763](https://github.com/aws/aws-sdk-ruby/issues/763).

* Feature - Aws::Resources::Resource - Added support for custom waiters
  with `Aws::Resources::Resource#wait_until`.

2.0.34 (2015-04-02)
------------------

* Feature - Aws::CodeDeploy - AWS CodeDeploy now supports OnPremises
  deployments.

* Feature - Aws::RDS - Adds the #describe_certificates API call, and changes
  other calls to show the current certificate.

* Feature - Aws::EC2 - Added the "D2" family of instance types.

* Feature - Aws::ElasticTranscoder - Adds support for PlayReady DRM.

* Issue - Endpoints - Resolved an issue where trailing slashes were lost from
  request parameter values that were bound to the end of the request uri path
  as a greedy placeholder.

  See [related GitHub issue #762](https://github.com/aws/aws-sdk-ruby/issues/762).

* Issue - Aws::SNS - Removed some incorrect identifiers from a few resource methods
  that were attempting to create `Aws::SNS::PlatformApplication` objects.

* Issue - Aws::Glacier - Corrected a typo in the `Aws::Vault#initiate_archive_retrieval`
  method.

* Issue - Aws::Errors - Improved the logic for extracting errors from HTTP responses
  to not fail when the body is empty or if the expected error XML or JSON is not
  present.

  Now falls back on providing a more generic service error based on the HTTP
  status code.

  See [related GitHub issue #755](https://github.com/aws/aws-sdk-ruby/pull/755).

* Feature - Resources - Added `#exists?` methods to a handful of resource classes.
  The `#exists?` method works by polling the exists wait for the resource exactly
  once. If the waiter is successful, then `true` is returned, if it fails, then
  `false` is returned.

  To add additional `#exists?` methods, a waiter must be added to the resource
  class as `Exists` and that waiter must be defined in the *.waiters.json
  document for that service.

* Issue - Presigned URLs - Resolved an issue where `x-amz-` headers were not
  being signed as part of a pre-signed URL.

* Feature - Aws::S3 - Added `Aws::S3::PresignedPost`, making it possible
  to generate a presigned post form for uploading a file from a browser
  directly to Amazon S3.

  ```ruby
  s3 = Aws::S3::Resource.new

  # post form for a specific object
  post = s3.bucket('bucket').object('key').presigned_post(options)
  post.fields
  #=> { ... }

  # force a key prefix
  post = s3.bucket('bucket').presigned_post(key_starts_with: '/uploads/')
  post.fields
  #=> { ... }
  ```

  See the API [PresignedPost documentation](http://docs.aws.amazon.com/sdkforruby/api/Aws/S3/PresignedPost.html)
  for more information and examples.

  See [related GitHub issue #720](https://github.com/aws/aws-sdk-ruby/issues/720).

2.0.33 (2015-03-26)
------------------

* Feature - Aws::ElasticTranscoder - Added support for job timing and input/output
  metadata.

* Feature - Aws::IAM - Add NamedPolicy to GetAccountAuthorizationDetails.

* Feature - Aws::IAM::Resouce - Added support for named policies.

* Feature - Aws::OpsWorks - Allow for customisation of BlockDeviceMapping on EC2
  instances launched through OpsWorks.

2.0.32 (2015-03-24)
------------------

* Feature - Aws::EC2 - Updated API reference documentation and add a few resource
  methods.

* Feature - Aws::S3 - Added support for Amazon S3 cross-region bucket replication.

2.0.31 (2015-03-19)
------------------

* Feature - Aws::S3 - Added support for requester pays headers to all supported
  object operations.

  See [related GitHub issue #694](https://github.com/aws/aws-sdk-ruby/issues/694).

* Feature - Aws::SNS - Added a utility class that can verify the authenticity
  of an SNS message.

  ```ruby
  verifier = Aws::SNS::MessageVerifier.new

  verifier.authentic?(message_body)
  #=> returns true/false

  verifier.authenticate!(message_body)
  #=> raises an error on bad message signature
  ```

  See [related GitHub issue #709](https://github.com/aws/aws-sdk-ruby/issues/709).

* Feature - Aws::SQS - Added `Aws::SQS::QueuePoller`, a utility class for
  long polling and processing messages from a queue.

  ```ruby
  poller = Aws::SQS::QueuePoller.new(queue_url)
  poller.poll do |msg|
    puts msg.body
  end # message deleted here
  ```

  See the [API reference documentation](http://docs.aws.amazon.com/sdkforruby/api/Aws/SQS/QueuePoller.html) for more examples.

* Feature - Aws::ElasticTranscoder - API update to support Applied Color SpaceConversion.

* Issue - Query Protocol - No longer returning nil for empty maps in
  query responses. `Aws::SQS::Client#get_queue_attributes` will always
  have a hash a `resp.attributes` instead of a possible `nil` value.

* Issue - Aws::EC2 - Fixed an issue with constructing `PlacementGroup`
  resources and added a missing `#delete` method to `RouteTable`.
  See related [GitHub pull request #737](https://github.com/aws/aws-sdk-ruby/issues/712).

* Issue - Aws::CloudSearchDomain - Resolved an issue with parsing error HTTP responses
  that have an empty body.

2.0.30 (2015-03-12)
------------------

* Feature - Aws::CloudTrail - Added support for the new Lookup feature.

* Feature - Aws::EC2 - Added pagination support to `Aws::EC2::Client#describe_snapshots`.

* Feature - Aws::CloudWatchLogs - Added support for ordering DescribeLogStreams
  results by LastEventTime.

* Feature - Aws::EC2 - Added `Aws::EC2::Snapshot#wait_until_completed` resource
  waiter. See relevant [GitHub issue #735](https://github.com/aws/aws-sdk-ruby/issues/735).

* Feature - Aws::EC2 - Added a `Aws::EC2::Volume#delete` method
  via [pull request](https://github.com/aws/aws-sdk-ruby/pull/731).

* Issue - Aws::CloudWatchLogs - Resolved an issue that would cause an infinite
  loop when paginating `Aws::CloudWatchLogs#get_log_events`.
  Resolves [GitHub issue #712](https://github.com/aws/aws-sdk-ruby/issues/712).

2.0.29 (2015-03-04)
------------------

* Feature - Aws::CognitoSync - Added for for streams. Amazon Cognito streams allows
  customers to receive near-realtime updates as their data changes as well as
  exporting historical data. Customers configure an Amazon Kinesis stream to receive
  the data which can then be processed and exported to other data stores such as
  Amazon Redshift.

* Feature - Aws::CloudSearch - Added support for manual scaling mode and m3
  instance types.

* Issue - Aws::CloudFormation - Added missing memberName traits to the resource
  models for the resource classes. This should squelch the duplicate attributes.

* Issue - Aws::STS - Resolved an issue that prevented `Aws::STS::Client` from making
  unatuthenticated requests to AssumeRoleWithSAML, and AssumeRoleWithWebIdentity.
  Resolves [GitHub issue #725](https://github.com/aws/aws-sdk-ruby/issues/725).

* Issue - Aws::S3 - Added support for pre-signed PUT object URLs with a content-type.
  Resolves [GitHub issue #721](https://github.com/aws/aws-sdk-ruby/issues/721).

2.0.28 (2015-02-24)
------------------

* Feature - Aws::RDS - Added an initial resource interface implementation.

* Feature - Aws::DataPipeline - Added support for tagging.

* Feature - Aws::Route53 - Added support for the new `ListHostedZonesByName` and
  `GetHostedZoneCount` operations.

2.0.27 (2015-02-20)
------------------

* Feature - Aws::ECS - Added support for volume mounts.

* Feature - Aws::ElasticTranscoder - Added support for warnings and cross region.

* Feature - Aws::Route53::Domains - Added paginators.

* Issue - Aws::S3 - Expanded logic for yielding data to a given block for
  more than just HTTP 200 response status codes. This is important so that
  206 responses can yield their data. This happens when using range headers.

2.0.26 (2015-02-17)
------------------

* Feature - API Documentation - Updated the
  [API reference documentation](http://docs.aws.amazon.com/sdkforruby/api/frames.html)
  to use the new framless Yard layout.

* Issue - Aws::STS - Reverted regionalized endpoints. Customers relying on the
  `Aws::STS::Client` using the 'sts.amazonaws.com' endpoint, even when specifying
  a region such as 'us-west-2' would likely receive errors when they are sent to
  the 'sts.us-west-2.amazonaws.com' endpoint. This is due to the new endpoints
  being opt-in via the AWS console. Reverting the change to prevent runtime-errors
  for customers that have not opted-in.

* Issue - Resource Waiters - Resolve an issue where the resource waiter
  methods were not yielding the waiter object like the client waiters do.
  This made it impossible to configure the delay and max attempts or
  callbacks.

2.0.25 (2015-02-17)
------------------

* Feature - Aws::SSM - Added support fot the new Amazon Simple Systems
  Management Service.

* Feature - Aws::CloudFront - Added support for the 2014-11-06 API version.

* Feature - Aws::STS - Added support for the regionalized enpoints.

* Issue - Aws::S3 - Resolved an issue where the `Aws::S3::Client` would raise
  an unhelpful error when credentials were not set. Now the client will
  raise an `Aws::Errors::MissingCredentialsError` when an API operation is
  called and credentials are not provided. Resolves
  [GitHub issue #708](https://github.com/aws/aws-sdk-ruby/issues/708).

* Issue - Aws::SharedCredentials - Now ignoring trailing whitespace from the
  access key id and secret access key values when loading the INI file.

* Upgrading - Aws::InstanceProfileCredentials - Increased the default number of
  attempts to make when loading credentials from the Amazon EC2 instance metadata
  service before failing from 0 to 5. Also increased the default open and read timeouts
  from 1 to 5 seconds each. This increases the reliability of loading instance
  credentials.

  **These new defaults only affect users who are constructing `Aws::InstanceProfileCredential`
  objects directly.** If you are using the default credential provider chain, the original
  defaults remain un-changed.

  To revert to default values:

  ```ruby
  Aws.config[:credentials] = Aws::InstanceProfileCredentials.new(
    retries: 0,
    http_open_timeout: 1,
    http_read_timeout: 1
  )
  ```

2.0.24 (2015-02-12)
------------------

* Feature - Aws::IAM - Added support for named and managed policies.

* Feature - Aws::Route53Domains - Added support for tagging operations.

* Feature - Aws::ElastiCache - Added support for tagging operations.

2.0.23 (2015-02-10)
------------------

* Feature - Aws::DynamoDB - Added support for the new index scan feature to
  `Aws::DynamoDB::Client`.

* Issue - Aws::S3 - Resolved an issue where calling `Aws::S3::Client#get_object`
  with a block would yield error data for non-200 HTTP responses.
  Resolves [GitHub issue #207](https://github.com/aws/aws-sdk-core-ruby/issues/207).

* Issue - CloudFormation - Resolved an issue with `Aws::CloudFormation` resource
  interfaces for accessing associated "has" associations. Fixes
  [GitHub issue #209](https://github.com/aws/aws-sdk-core-ruby/issues/209).

* Issue - Resource Waiters - Resolved an issue with resource waiters raising
  an error after success. Introduced v2.0.19.pre. Resolves
  [GitHub issue #210](https://github.com/aws/aws-sdk-core-ruby/issues/210).

2.0.22 (2015-02-05)
------------------

* Issue - Aws::EC2 - Fixed the return value from
  `Aws::EC2::Resource#create_volume`.

* Upgrading - Aws::SQS::Resources - Removed resource interfaces for
  `Aws::SQS`. These are unstable, so they have been removed for the public
  release of `aws-sdk-resources`.

* Feature - Aws::S3::Presigner - Added a `:secure` option to `#presigned_url`,
  which defaults to `true`. When `false`, the presigned URL will use HTTP and
  not HTTPS.

2.0.21 (2015-01-27)
------------------

* Feature - Aws::ImportExport - Added support for the new get shipping
  label API operation.

* Feature - Aws::DynamoDB - Added support for online indexing.

* Issue - Response Stubbing - Resolved an issue where the response stubbing
  plugin would not correctly populate the response target for streaming API calls,
  such as `Aws::S3::Client#get_object`. Fixes GitHub issue #197.

* Issue - Aws::EC2 - Added missing paginator configuration for
  `Aws::EC2::Client#describe_instances`. Fixes GitHub issue #196.

2.0.20 (2015-01-20)
------------------

* Feature - Aws::EMR - Added support for Custom security groups feature for
  Amazon Elastic MapReduce (EMR). Custom security groups allow you to define
  fine grain control over the security rules for your EMR cluster by specifying
  your own Amazon EC2 security groups.

* Feature - Aws::CognitoIdentity - Amazon Cognito now allows developers to save
  the association between IAM roles and an identity pool via the
  `#set_identity_pool_roles` API. Identity pools with IAM roles associated can
  use the new `#get_credentials_for_identity` API to retrieve a set of temporary
  credentials based on the associated roles. Roles associated to the pool must
  be properly configured to work with Amazon Cognito. Developers can still use
  the `#get_open_id_token` API in conjunction with STS
  `#assume_role_with_web_identity` API if there are no roles associated with
  their pool.

* Feature - Aws::AutoScaling - Added support to `Aws::AutoScaling::Client` for
  classic link.

2.0.19 (2015-01-15)
------------------

* Feature - Aws::ElasticTranscoder - Updated `Client` to support the new
  encryption features.

* Feature - Waiters - Added support for version 2 of the waiters
  definition format.

* Issue - Pagingation - Resolved an issue where an empty hashes or arrays
  were being treated as valid next tokens for paging response. This appears
  to only have affected `Aws::DynamoDB::Client#batch_get_item`.

* Issue - Instance Profile Credentials - Resolved an issue where instance
  profile credentials would never load if the first attempt fails.
  See GitHub issue #193.

* Issue - Aws::S3::Object - Resolved an issue with `#upload_file` where
  options intended for `#initiate_multipart_upload` are being passed along
  to individual `#upload_part` API calls. Fixes
  [GitHub issue #684](https://github.com/aws/aws-sdk-ruby/issues/684).

2.0.18 (2015-01-08)
------------------

* Feature - Aws::CloudHsm - Added support for the new Amazon CloudHSM
  service.

* Feature - Aws::EC2 - Added support for the new Amazon EC2 Container
  Service.

* Feature - Aws::EC2 - Added support for Amazon EC2 classic link.

* Feature - Aws::RDS - Updated to the latest API version, 2014-10-31, adding
  support for the new `#describe_pending_maintenance_actions` operation to
  `Aws::RDS::Client`.

* Issue - Aws::S3 - Resolved an issue with `Aws::S3::Client#get_bucket_location`.
  Fixes GitHub issue #185.

* Upgrading - Aws::IAM - Moved `Aws::Role#update_assume_role_policy` to a
  new resource class.

  ```ruby
  iam = Aws::IAM::Resource.new

  # old
  iam.role('name').update_assume_role_policy(policy_document:'...')

  # new
  iam.role('name').assume_role_policy.update(policy_document:'...')
  ```

* Upgrading - Aws::IAM - Renamed two methods on `Aws::IAM::MFADevice`.

  * `#enable` -> `#associate`
  * `#disable` -> `#disassociate`

* Upgrading - Aws::IAM - Removed `Aws::IAM::AccountSummary`. Calling
  `Aws::IAM::Resource#create_account_summary` no longer returns a resource
  object.

  ```ruby
  iam = Aws::IAM::Resource.new

  # old
  iam.create_account_alias(account_alias:'alias')
  iam.account_alias('alias').delete

  # new (no need to specify the alias when deleting)
  iam.create_account_alias(account_alias:'alias')
  iam.delete_account_alias
  ```

  Also, the `Aws::IAM::Resource#account_aliases` method has been removed.
  There is no replacement. IAM does not permit more than one account
  alias.

2.0.17 (2014-12-22)
------------------

* Issue - Request Marshalling - Resolved an issue with how blob types
  are marshalled into a JSON request body. Resolved by #180, fixes
  #178, #182.

2.0.16 (2014-12-18)
------------------

* Feature - Aws::SWF - Added support for task priorities.

* Feature - Aws::S3::Encryption::Client - Added new options to
  `Aws::S3::Encryption::Client#get_objet`. You can now pass the
  following options to `#get_object`:

  * `:envelope_location`
  * `:instruction_file_suffix`

  Passing these options override the default values determined
  during construction of the `Aws::S3::Encryption::Client`.

2.0.15 (2014-12-17)
------------------

* Feature - Client-Side Encryption - Added `Aws::S3::Encryption::Client`
  which allows users to perform PUT and GET Object operations against
  Amazon S3 while encrypting and decrypting the object client-side.

  ```ruby
  require 'openssl'
  key = OpenSSL::Cipher.new("AES-256-ECB").random_key

  # vanilla S3 client
  s3 = Aws::S3::Client.new(region:'us-west-2', credentials: ...)

  # encryption client
  s3e = Aws::S3::Encryption::Client.new(encryption_key: key, client: s3)

  # round-trip an object, encrypted/decrypted locally
  s3e.put_object(bucket:'aws-sdk', key:'secret', body:'handshake')
  s3e.get_object(bucket:'aws-sdk', key:'secret').body.read
  #=> 'handshake'

  # attempt to read the encrypted object without the encryption client
  s3.get_object(bucket:'aws-sdk', key:'secret').body.read
  #=> "... cipher text ..."
  ```

  You can configure a `:key_provider` to the encyrption client to allow
  for using multiple decryption keys. See the `Aws::S3::Encryption::Client`
  API documentation for more information.

* Feature - Async Handlers - The HTTP handler now signals the response
  object on HTTP headers, data, complete and errors. This allows downstream
  handlers to handler HTTP events as they happen. As a result, all
  handlers calling `#on(...)` on the response object no longer need
  to block on the HTTP response being complete.

* Feature - Aws::DynamoDB - Now calculating a CRC32 checksum of the
  HTTP response body on every request and comparing it against the
  value returned by Amazon DynamoDB in the `X-Amz-Crc32` header.
  Failed checksums trigger an automatic retry.

  You can disable this checksum by passing `compute_checksums: false`
  to the Aws::DynamoDB::Client constructor:

  ```ruby
  ddb = Aws::DynamoDB::Client.new # verify crc32 checksums
  ddb = Aws::DynamoDB::Client.new(compute_checksums: false) # disabled
  ```

* Feature - Aws::EMR - Added support for accessing the new normalized
  instance hours attribute.

* Feature - Aws::StorageGateway - Added support for for the new ResetCache
  API operation.

2.0.14 (2014-12-12)
------------------

* Feature - Aws::ElasticTranscoder - Added support for user metadata to
  `Aws::ElasticTranscoder::Client`.

* Feature - Aws::Glacier - Added support for data retrieval policies to
  `Aws::Glacier::Client`.

* Upgrading - Aws::SNS - Removed `:topic_arn` from the list of identifiers
  required to construct an `Aws::SNS::Subscription`. Subscription is no
  longer a sub-resource of Topic.

  ```ruby
  sns = Aws::SNS::Resource.new

  # old
  subscription = sns.topic(topic_arn).subscription(subscription_arn)

  # new
  subscription = sns.subscription(subscription_arn)
  ```

2.0.13 (2014-12-08)
------------------

* Feature - Aws::SQS - Added support for purging queues.

* Feature - Aws::OpsWorks - You can now use AWS OpsWorks with existing EC2
  instances and on-premises servers.

* Issue - Aws::CloudFront - Resolved an issue that prevented the client
  paginators from working. Fixes #169.

2.0.12 (2014-12-04)
------------------

* Feature - Aws::S3 - Added a `#public_url` method to `Aws::S3::Object`.
  Use this method to generate a URL that can be pasted into the
  browser for objects with a "public-read" ACL.

  ```ruby
  s3 = Aws::S3::Resource.new(region:'us-west-2')
  s3.bucket('aws-sdk').object('key').public_url
  #=> "https://aws-sdk.s3-us-west-2.amazonaws.com/key"
  ```

* Feature - Aws::Route53 - Added support to `Aws::Route53::Client` for the new
  UpdateHostedZoneComment operation.

* Feature - Aws::IAM - Added support to `Aws::IAM::Client` for the new
  GetAccountAuthorizationDetails operation.

* Feature - Aws::Kinesis - Added support to `Aws::Kinesis::Client` for the new
  PutRecords operation.

  ```ruby
  kinesis = Aws::Kinesis::Client.new
  kinesis.put_records(stream_name: '...', records:[...])
  ```

* Issue - Aws::STS::Client - Resolved an issue with the endpoint used for Gov Cloud.
  Resolves issue #167.

* Issue - Persistent Connections - Resolved an issue that prevented Aws::S3::Client
  from re-using HTTP connections.

2.0.11 (2014-11-26)
------------------

* Feature - Aws::ElasticTranscoder - Added support for encyption using
  AWS Key Management Service.

* Feature - Aws::DataPipeline - Added support for templates.

* Issue - Aws::S3 - Resolved an issue where object keys with plus
  signs were incorrectly un-marhsalled from the XML response when
  keys are URL encoded. Fixes #161.

* Upgrading - Aws::IAM - Renamed `Aws::IAM::User#create_access_key`
  to `#create_access_key_pair`. Also returning an AccessKeyPair
  now instead of an AccessKey.

  ```ruby
  key_pair = iam.user('username').create_access_key_pair
  key_pair.id     #=> access key id
  key_pair.secret #=> secret access key
  ```

2.0.10 (2014-11-20)
------------------

* Feature - Aws::Redshift - Added support for AWS Key Management Service (KMS)
  integration.

* Issue - Aws::KMS - Corrected the paginators for `Aws::KMS::Client`.

* Issue - Aws::IAM - Corrected the signature version 4 signing name when
  using `Aws::IAM::Client` in the `us-gov-west-1` region.
  Fixes #159

* Issue - Aws::S3 - Resolved an issue when making a GET Object request against
  an object encrypted with AWS KMS. Automatically using signature version 4
  for GET requests in all known regions. When the region is not known, a best
  effort is made to determine the region. Fixes #157.

* Issue - Aws::S3 - The `Aws::S3::Client#get_bucket_location` operation will no
  longer use DNS addressing and will instead use path-addressing for the bucket
  name. This avoids signature errors for v4-only regions when the classic
  endpoint is used with a v2 signature.

* Issue - Aws::S3 - Fixed a bug in `#put_object_acl` serialization.
  Fixes #160.

2.0.9 (2014-11-13)
------------------

* Feature - New Service - Added support for Amazon Lambda.

  ```ruby
  lambda = Aws::Lambda::Client.new
  ```

* Feature - Aws::S3 - Added support for event notifications to
  `Aws::S3::Client`.

* Feature - Aws::ConfigService - Added paginators for
  `Aws::ConfigService::Client#get_resource_config_history`.

2.0.8 (2014-11-12)
------------------

* Feature - New Service - Added support for AWS CodeDeploy.

  ```ruby
  cd = Aws::CodeDeploy::Client.new
  ```

* Feature - New Service - Added support for Amazon Key Management Service (KMS).

  ```ruby
  kms = Aws::KMS::Client.new
  ```

* Feature - New Service - Added support for AWS Config.

  ```ruby
  cfg = Aws::ConfigService::Client.new
  ```

* Feature - Aws::S3 - Added support for server-side encryption using
  Amazon Key Management Service (KMS).

* Feature - Aws::CloudFormation - Added support for the new GetTemplateSummary
  API call via `Aws::CloudFormation::Client#get_template_summary`.

* Feature - Aws::EC2::Instance - Added `#decrypt_windows_password` method
  to the Instance class.

* Feature - Aws::CloudTrail - Added support for Amazon CloudWatch logs delivery.

* Feature - Aws::EC2 - Added support for EBS encryption with Amazon KMS.

2.0.7 (2014-11-06)
------------------

* Feature - Aws::CognitoSync - Added support for push sync.

* Feature - Aws::Route53 - Added support for private DNS. Also,
  `Aws::Route53::Client` now defaults to signature version 4.

* Feature - Aws::CloudFront - Added support for the 2014-10-21 API version.

* Upgrading - `Aws::OpsWorks::Stack` - Renamed `#get_summary` to
  `#summary`.

* Feature - `Aws::S3::Bucket` - Added a `#url` method that returns
  the public URL for the bucket.

* Issue - JSON Protocol - Resolved an issue with serializing JSON requests
  where the operation does not model any input.

* Issue - `Aws::S3::ObjectVersion` - The `#get` and `#head` methods were incorrectly
  marked as batch actions instead of instance actions.

2.0.6 (2014-10-30)
------------------

* Feature - Amazon S3 - Added resource classes for object versions.
  Also added `Aws::S3::Bucket#delete!` using the batch delete feature
  of `Aws::S3::ObjectVersion`.

  ```ruby
  bucket = S3::Resource.new.bucket('aws-sdk-versioned')

  # enumerate every versioned object in a bucket
  bucket.object_versions.each do |obj_version|
    # ...
  end

  # delete every object version in a bucket (two options)
  bucket.object_versions.delete
  bucket.clear!

  # delete a bucket and all of its objects
  bucket.delete!
  ```

* Feature - Amazon S3 - You can now pass
  `:compute_parts => true` to `Aws::S3::MultipartUpload#complete` and the
  part list will be computed automatically by calling `#list_parts` on
  the client.

  ```ruby
  s3 = Aws::S3::Resource.new
  upload = s3.bucket('bucket-name').object('key').multipart_upload('id')
  obj = upload.complete(compute_parts:true)
  ```

* Feature - `Aws::EC2::Instance` Waiters - Added the following methods to
  the instance resource class:

  * `#wait_until_running`
  * `#wait_until_stopped`
  * `#wait_until_terminated`

* Feature - SQS Batch Message Delete - `Aws::SQS::Message` objects can
  now be deleted in batches. See #141.

  ```ruby
  messages = queue.receive_messages(max_number_of_messages: 5)
  # do something with messages ...
  messages.delete # delete the batch of messages in a single request
  ```ruby

* Feature - Batch Resource Actions - Added the following operations that can
  be called on collections or batches of `Aws::EC2::Instance` objects:

  * `#create_tags`
  * `#monitor`
  * `#unmonitor`
  * `#start`
  * `#stop`
  * `#reboot`
  * `#terminate`

  Also added `Aws::EC2::Tag::Batch#delete`.

* Upgrading - Dependencies - Replaced JMESPath library dependency from the
  `jamespath` gem to the newer `jmespath` gem. The `jmespath` gem has full
  compliance with the JMESPath spec.

2.0.5 (2014-10-23)
------------------

* Feature - Amazon EC2 - Added support for the 2014-09-01 API version. Also
  expanded coverage of resource interfaces, waiters, and paginators.

* Issue - Amazon EC2 - Now defaulting to signature version 4 so that
  `Aws::EC2::Client` works in the new 'eu-central-1' region without extra
  configuration.

2.0.4 (2014-10-23)
------------------

* Feature - Paginators - Added support for paging `Aws::EC2::Client#describe_volumes`.

* Feature - Route53 Domains - Added support for auto-renew domains.

* Feature - Open-ID Connect - Added support for Open-ID Connect to
  `Aws::IAM::Client` and `Aws::EC2::Client`.

* Feature - Queue Attributes - `Aws::SQS::Queue` now has getter methods for
  queue attributes that return type-casted values from the `#attributes`
  hash.

  ```ruby
  # before
  queue.attributes['CreatedTimestamp'] #=> '1413825896' (epoc seconds)
  queue.attributes['DelaySeconds'] #=> '60' (string seconds)
  queue.attributes['QueueArn'] #=> 'arn'

  # after
  queue.created_timestamp #=> 2014-10-20 10:24:56 -0700 (Time obj)
  queue.delay_seconds #=> 60 (integer seconds)
  queue.arn #=> 'arn' (without extra queue_ prefix on method)
  ```

* Feature - Assume Role Credentials - Added a credentials class that assumes
  a role via `Aws::STS::Client#assume_role`. Credentials are automatically
  refreshed before the expire:

  ```ruby
  credentials = Aws::AssumeRoleCredentials.new(
    role_arn: 'role-arn',
    role_session_name: 'session-name',
    client: Aws::STS::Client.new(...)
  )
  ```

* Issue - Aws::PageableResponse - Calling `#count` on a pageable response
  was triggering n API calls to get the number of response pages. Instead
  it raises an error now, unless the wrapped data object responds to
  count. Fixes #106.

2.0.3 (2014-10-16)
------------------

* Feature - Amazon RDS - Added support for the 2014-09-01 API version.

* Feature - Pre-signed URLs - Added the ability to generate presigned
  URLs from an `Aws::S3::Object`.

      s3 = Aws::S3::Resource.new
      obj = s3.bucket('aws-sdk').object('key').

      obj.presigned_url(:get)
      #=> "https://bucket-name.s3.amazonaws.com/object-key?..."

  URLs expire in 15 minutes by default. You can configure the
  expiration period and provide additional params:

      # url expires in one hour, the file upload will be
      # publicly accessible
      obj.presigned_url(:put, expires_in: 3600, acl: 'public-read')

* Feature - HTTP - Added the ability to configure the CA Cert store
  for HTTPs requests.

      Aws.config[:ssl_ca_store] = OpenSSL::X509::Store.new

* Feature - Waiters - Added waiters for Amazon EC2 instance
  instance and system status.

* Feature - Waiters - Added waiters for Amazon EC2 image availability.

* Feature - Waiters - Added `:job_complete` waiter to
  Aws::ElasticTranscoder::Client.

* Feature - Aws::CloudSearch::Client - Added support for configuring advanced
  Japanese language processing.

* Issue - Resolved an issue with the query serializer sending `nil`
  values. See #133.

2.0.2 (2014-10-08)
------------------

* Feature - DynamoDB Document Attributes - Added support for hashes,
  arrays, booleans, and nil to DynamoDB attribute values.

      ddb.put_item(table_name:'name', item: {
        id: 123,
        complex: {
          attributes: true
          lists: ['with', { mixed: true }, 123, 'data'],
        }
      })

* Feature - S3::Object#upload_file - Added a utility method for uploading
  files to Amazon S3. This method automatically manages uploading large
  objects using the multipart upload APIs.

  ```ruby
  s3 = Aws::S3::Resource.new
  s3.bucket('aws-sdk').object('key').upload_file('/path/to/file')
  ```

* Feature - Instance Profile Credentials - Added the ability to configure the
  number of attempts to make when loading instance profile credentials.

  ```ruby
  # makes at most 4 attempts to load credentials
  InstanceProfileCredentials.new(retries:3)
  ```

* Feature - Stubbing Client Responses - Added support for stubbing
  client responses without making API calls. In addition to the
  default fake resposnes, you can provide stub data and errors:

  ```ruby
  s3 = Aws::S3::Client.new(stub_responses: true)
  s3.stub_responses(:list_buckets, buckets:[{name:'aws-sdk'}])
  s3.list_buckets.buckets.map(&:name)
  #=> ['aws-sdk']
  ```

  See API docs for `Aws::Client#stub_responses` for more information.

* Feature - S3 Waiters - Added resource waiters for `S3::Bucket` and
  `S3::Object`.

* Issue - Aws::SQS::Client#receive_message - Resolved an issue where
  the response from `#receive_message` could return `nil` for `#messages`.
  It now always returns an array.

* Issue - Waiters - Resolved an issue with `Aws::EC2::Client#wait_until`
  and the volume status waiters. These were polling the incorrect API
  method causing `#wait_until` to raise an error. See issue #129.

2.0.1 (2014-09-29)
------------------

* Feature - Aws::CognitoIdentity::Client - Added support for Cognito developer identities.

* Issue - Region - Now giving a helpful error message when a region is
  not configured. Was previously exposing a URI parse error message.

2.0.0 (2014-09-25)
------------------

* Feature - S3 Presigned URLs - Added support for pre-signed URLs for
  Amazon S3.

  ```ruby
  presigner = Aws::S3::Presigner.new
  presigner.presigned_url(:put_object, bucket:'aws-sdk', key:'key')
  #=> "https://aws-sdk.s3.amazonaws.com/key?..."
  ```

* Feature - Autoload - Service modules / classes now autoload upon first
  use. This speeds up gem load time significantly.

* Feature - Resources - Resource constructors now pass additional
  options to the Client constructor when `:client` is not provided.

* Issue - SigV4 - Resolved an issue with how version 4 signatures generated
  the normalized querystring. Query params without a value now consistantly
  receive a trailing `=`.

* Issue - SigV4 - Resolved an issue with how version 4 signatures generated
  the normalized querystring. Query params are now sorted by name.

2.0.0.rc16 (2014-09-11)
------------------

* Feature - Waiters - Added a new feature called waiters. Waiters poll a
  single API until a certain condition is matched. Waiters are invoked
  by a name and can be configured.

      # client waiter
      ec2 = Aws::EC2::Client.new
      ec2.wait_until(:instance_stopped, instance_ids:['i-12345678'])

      # resource waiters
      instance = Aws::EC2::Instance.new(id:'i-12345678')
      instance.start
      instance.wait_until_running

  See the client API documentation for `#wait_until` and the resource API
  documentation for `#wait_until_{condition}`.

* Feature - Resources - Merged in resources branch. You can now use
  resource-oriented interfaces with `Aws::S3`, `Aws::EC2`, `Aws::SQS`,
  `Aws::SNS`, `Aws::Glacier`,
  and `Aws::IAM`.

* Feature - New Gems - Organized the repository into three gems, `aws-sdk`,
  `aws-sdk-resources`, and `aws-sdk-core`. Moved library and test files
  each into their respective gem directories.

* Issue - Error Class Names - Resolved an issue that could raise a
  `NameError`, "wrong constant name" at runtime in response to a service
  error. Fixes #97.

* Issue - `Aws::CloudSearchDomain::Client` - Resolved an issue that would cause
  the `Aws::CloudSearchDomain::Client` constructor to raise an error when
  `Aws.config[:region]` is set. Fixes #103.

* Issue - Shared Credentials - Resolved an issue where an error was raised
  if the shared AWS credentials file is present and is empty or missing the
  default profile. Fixes #104.

2.0.0.rc15 (2014-08-14)
-----------------------

* Feature - `Aws::S3::Client` - Enabling url-encoding of Amazon S3 keys by default.
  Keys are decoded before response data is returned. See [#95](https://github.com/aws/aws-sdk-core-ruby/issues/95).

* Feature - `Aws::ElasticLoadBalancing::Client` - Added support for the new tagging
  operations.

* Feature - `Aws::CloudSearch::Client` - Will now sign requests when credentials
  are provided. You can continue making unauthenticated requests if you do not
  configure credentials to the client.

* Feature - Coverage - Now generating coverage reports during Travis builds
  and reporting via Coveralls.io.

* Upgrading - `Aws::DynamoDB::Client` - Added a plugin that simplifies working
  with DynamoDB attribute values. Enabled by default, to restore default
  behavior, use `:simple_attributes => false`.

* Issue - Documentation - Now loading API files with UTF-8 encoding.
  See [#92](https://github.com/aws/aws-sdk-core-ruby/issues/92).

2.0.0.rc14 (2014-08-05)
-----------------------

* Upgrading - Client Classes - Versioned client classes removed, e.g.
  `Aws::S3::Client::V20060301.new` is now `Aws::S3::Client.new` The
  `:api_version` constructor option is no longer accepted.

* Upgrading - `Aws` Module - Helper methods on `Aws` for client classes
  deprecated; For example, calling `Aws.s3` will generate a deprecation
  warning. Use `Aws::S3::Client.new` instead. Helpers will be removed as
  of v2.0.0 final.

* Upgrading - Config - When configuring an `:endpoint`, you must now specify
  the HTTP scheme, e.g. "http://localhost:3000", instead of "localhost:3000".

2.0.0.rc1 - 2.0.0.rc13
----------------------

* No changelog entries.
