# Introduction to S3:

- S3 (Simple Storage Service) allows storing objects or files in buckets.
- Buckets require globally unique names.
- Objects have keys, representing their full path.
- Maximum object size is 5 terabytes.
- Object tags (key-value pairs) aid in data classification, security, and lifecycle management.

## Role in Machine Learning:

- S3 serves as the backbone for many AWS ML services like SageMaker.
- Facilitates the creation of a Data Lake.
- Offers infinite sizing without provisioning.
- Provides 99.999999999% durability.
- Decouples storage from compute, enabling scalability.
- Centralized architecture.
- Supports any file format, making it suitable for a data lake.

## Data Partitioning:

- A pattern to speed up range queries, useful for services like Amazon Athena.
- Data partitioning involves organizing data into folders based on criteria like date or product.
- By Date: s3://bucket/my-data-set/year/month/day/hour/data_00.csv
- By Product: s3://bucket/my-data-set/product-id/data_32.csv
- Partitions help quickly find relevant datasets for specific queries.
- Tools like Kinesis aid in data partitioning.

## Creating an S3 Bucket :
- Sign in to AWS Console
- Navigate to Amazon S3
- Click on "Create Bucket" [bucket creation process]
- Enter Bucket Name and Region
    - Remember that bucket names must be globally unique across all of AWS.
- Configure Bucket Options
    - Leave other configurations as default.
    - You can configure advanced settings later if needed.

- Click "Create Bucket" [To complete creation process] 
- Create Folders Within the Bucket
- Upload Data

# Storage Classes Overview:

Amazon S3 offers several storage classes including Standard - General Purpose, Infrequent Access, One Zone - Infrequent Access, Glacier Instant Retrieval, Glacier Flexible Retrieval, Glacier Deep Archive, and Intelligent Tiering.
Understanding these classes is essential for the exam.

## Object Storage Class Selection:

When creating an object in Amazon S3, you can choose its storage class.
You can also manually modify the storage class or use S3 Lifecycle configurations to automate object movement between classes.

## Durability and Availability:

- **Durability** refers to the likelihood of object loss by Amazon S3
    - If you store 10 million objects on Amazon S3, you can expect to lose a single object once every 10,000 years.
- **Availability** indicates how readily a service is accessible, varying based on the storage class chosen.
    - For example, S3 Standard has a 99.99% availability. That means that about 53 minutes a year, the service is not going to be available.

## Storage Class Details:

1. **S3 Standard**: 
    - 99.99% availability
    - Used for frequently accessed data
    - Low latency, high throughput
    - **Use Cases**:
        - Big data analytics
        - Mobile and gaming application
        - Content distribution.
1. **S3 Standard-Infrequent Access (IA)**: 
    - Lower cost than Standard
    - Suitable for less frequently accessed data, with costs incurred on retrieval.
    - **Use Cases**:
        - Disaster recovery
        - Backups
1. **S3 One Zone - Infrequent Access**: 
    - High durability within a single Availability Zone (AZ)
    - Lower availability compared to S3 IA.
    - **Use Cases**:
        - Store secondary copy of backups of maybe on premises data, or data you can recreate.
1. **Glacier Storage Classes**: 
    - Low-cost object storage for archiving and backup purposes.
    1. **Glacier Instant Retrieval**: Milliseconds retrieval for quarterly accessed data.
    1. **Glacier Flexible Retrieval**: Offers expedited, standard, or bulk retrieval options with varying retrieval times.
    1. **Glacier Deep Archive**: Meant for long-term storage, with options for standard and bulk retrieval.
1. **S3 Intelligent-Tiering**: Automatically moves objects between Access Tiers based on usage patterns, incurring monitoring and auto-tiering fees.

    - **Frequent Access Tier**: Automatic default tier for frequently accessed objects.
    - **Infrequent Access Tier**: For objects not accessed for 30 days.
    - Archive Instant Access Tier: Automatic tier for objects not accessed for 90 days.
    - **Archive Access Tier**: Optional tier for objects not accessed from 90 to over 700 days.
    - **Deep Archive Access Tier**: Optional tier for objects not accessed from 180 to over 700 days.

## Comparison of Storage Classes:

Durability remains consistent across all classes, while availability varies.
Understanding the minimum storage duration and pricing charts can aid in selecting appropriate storage classes.

![Storage Classes](/Study%20Notes/1.%20Data%20Engineering/resources/S3%20Storage%20Classes%20Comparison.png)


## Steps to Select Storage Class:

1. Create a new bucket in Amazon S3.
1. Upload an object to the bucket.
1. Click on the uploaded object to view its properties.
1. Under "Storage Class," select the desired storage class from the available options.
1. Choose the appropriate storage class based on your requirements, such as Standard, Intelligent-Tiering, Standard-IA, One Zone-IA, Glacier Instant Retrieval, Glacier Flexible Retrieval, or Glacier Deep Archive.


## How to Change Storage Class:

1. Navigate to the properties of the uploaded object in your S3 bucket.
1. Scroll down to find the "Storage Class" option.
1. Click on "Edit" or a similar option to modify the storage class.
1. Select the new storage class from the available options, such as One Zone-IA, Glacier Instant Retrieval, Glacier Flexible Retrieval, or Intelligent-Tiering.
1. Save the changes to apply the new storage class to the object.

## Steps for Lifecycle Rules and Review:

1. Go to your S3 bucket management settings.
1. Navigate to "Lifecycle rules" and create a new rule.
1. Provide a name for the rule, such as "Demo Rule."
1. Specify whether the rule applies to all objects in the bucket.
1. Define the lifecycle transitions for the objects, such as moving to Standard-IA after 30 days, Intelligent-Tiering after 60 days, or Glacier Flexible Retrieval after 180 days.
1. Review the configured lifecycle transitions to ensure they meet your requirements.
1. Save the lifecycle rule to automate the movement of objects between storage classes based on defined criteria.

## Moving between Storage Classes 

- Objects in Amazon S3 can be transitioned between different storage classes to optimize cost and performance.

![Moving Storage Classes](/Study%20Notes/1.%20Data%20Engineering/resources/Moving%20Storage%20Classes.png)

## Automation with Lifecycle Rules:

- Lifecycle rules automate the transition of objects between storage classes based on defined criteria.
- Transition actions configure objects to move to another storage class after a specified time, such as moving to Standard IA after 60 days or Glacier after six months.
- Expiration actions delete objects after a certain period, applicable for access log files or incomplete multipart uploads.
- Rules can be applied to entire buckets or specific paths within buckets.
- Rules can also be targeted based on object tags, allowing for granular control over lifecycle management.

### Example Question 

> Your application on EC2 creates images thumbnails after profile photos are uploaded to Amazon S3. These thumbnails can be easily recreated, and only need to be kept for 60 days. The source images should be able to be immediately retrieved for these 60 days, and afterwards, the user can wait up to 6 hours. How would you design this?

- S3 source images can be on Standard, with a lifecycle configuration to transition them to Glacier after 60 days
- S3 thumbnails can be on One-Zone IA, with a lifecycle configuration to expire them (delete them) after 60 days

### How do we determine what's the optimal number of days to transition an object from one class to another?

- S3 Analytics provides recommendations for optimizing lifecycle rules, particularly for Standard and Standard IA classes.
- Reports generated by S3 Analytics help in determining the optimal number of days to transition objects between storage classes.

# Amazon S3 - Security 

1. **User-Based Security**:
   - IAM policies authorize API calls for IAM users.

2. **Resource-Based Security**:
   - Utilize S3 Bucket policies to grant access to users or other AWS accounts.
   - Allows for cross-account access and making Buckets public.

3. **Access Control Lists (ACLs)**:
   - Provide finer-grained security but are less common.
   - Can be set at object or Bucket level.

4. **Bucket Policies**:
   - JSON-based policies determine access to Buckets and objects.
   - Specify allowed or denied actions for principles (users/accounts).
   - Example: Allowing public access with "GetObject" action.
           
        ![JSON Policy](/Study%20Notes/1.%20Data%20Engineering/resources/JSON%20Policy.png)

5. **Object Encryption**:
   - Secure objects using encryption keys for added security.

6. **Block Public Access**:
   - Prevents public access even if Bucket policies allow it.
   - Adds an extra layer of security to prevent data leaks.

7. **Use Cases**:
   - Grant access to IAM users through policies.
   - Use IAM roles for EC2 instances to access S3.
   - Allow cross-account access with Bucket policies.
   - Utilize Block Public Access settings for added security.

## Steps for Bucket Policies

1. **Allow Public Access**:
   - Go to Bucket Permissions tab.
   - Edit bucket settings to allow public access.
   - Confirm the action, understanding the risks.

2. **Create Bucket Policy**:
   - Scroll down to the Bucket Policy section.
   - Use the Policy Generator tool.
   - Select "Allow" as the action.
   - Set the Principal to "*" to allow anyone.
   - Choose "GetObject" as the action.
   - Specify the Amazon Resource Name (ARN) as the bucket name with "/*" to cover all objects.

3. **Apply Bucket Policy**:
   - Copy the generated policy.
   - Paste it into the Bucket Policy editor.
   - Save the changes.

4. **Verify Public Access**:
   - Confirm that permissions now show as public.
   - Access the object using the public URL.
   - Verify that the object is accessible from the internet.

* **Note**:
   - Understand the implications of making a bucket public.
   - Use caution when allowing public access to S3 objects.
   - Utilize Bucket policies to control access to objects in S3.