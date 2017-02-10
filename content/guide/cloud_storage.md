+++
draft = false
title = "Cloud Storage (GCS)"
categories = ["Storage and Databases"]

[[owners]]
  email = "bookman@google.com"
  name = "Colin Bookman"
+++

Google Cloud Storage (GCS) is unified object storage for developers and enterprises,
from live data serving to data analytics/ML to data archiving.
<!--more-->

GCS is designed for 99.999999999% durability.  Data is stored redundantly, 
with automatic checksums to ensure data integrity. There's a greater chance 
that the Earth will be hit by an asteroid than GCS losing data
[[2]](#references)[[1]](#references).

GCS is practically infinitely scalable. Cloud Storage can handle small to multi-exabyte datasets. 
Thing of GCS as a Durable, Scalable, Available, and Consistent Object Storage. 

All GCS storage classes offer the same throughput, low latency (time to first byte typically tens 
of milliseconds), and high durability. The classes differ by their availability, minimum storage 
durations, and pricing for storage and access.

For Best Practices please refer to [Best Practices for Cloud Storage Best 
Practices](https://cloud.google.com/storage/docs/best-practices).


# Buckets [[4]](#references)

Buckets are the basic containers that hold your data. Everything that you store in Google Cloud 
Storage must be contained in a bucket. You can use buckets to organize your data and control access 
to your data, but unlike directories and folders, you cannot nest buckets.

When you [create a bucket](https://cloud.google.com/storage/docs/creating-buckets), you can specify 
a name, default [storage class](https://cloud.google.com/storage/docs/storage-classes), and 
[geographic location](https://cloud.google.com/storage/docs/bucket-locations) for the bucket. The 
storage class you choose will determine the availability, throughput, and storage cost of objects 
uploaded to the bucket.


## What's Mutable [[4]](#references)

After a bucket is created, the [storage class can be 
changed](https://cloud.google.com/storage/docs/changing-storage-classes#bucket) to any class that 
is allowable, given the bucket's location. However, the bucket name and location can only be 
changed by [deleting and re-creating the 
bucket](https://cloud.google.com/storage/docs/moving-buckets).


## Naming Considerations [[4]](#references)

Bucket names have more restrictions than object names, because every bucket resides in a single 
Google Cloud Storage namespace. Also, bucket names can be used with a CNAME redirect, which means 
they need to conform to DNS naming conventions. For a list of what bucket names are allowed, see 
the [bucket naming guidelines](https://cloud.google.com/storage/docs/naming#requirements)..


# Objects [[4]](#references)

Objects are the individual pieces of data that you store in Google Cloud Storage. There is no limit 
on the number of objects that you can create in a bucket. A single object can be up to 5 TB in 
size. Objects have two components: object data and object metadata. 

The object data component is the file's raw data, and the object's metadata is a collection of name 
value pairs that describe various object qualities. Part of the object metadata is the Object name. 


## Object Name [[4]](#references)

An object name is just metadata to Google Cloud Storage. Object names can contain any combination 
of Unicode characters (UTF-8 encoded) less than 1024 bytes in length. 

GCS stores objects in a key (object name) value (object data) data structure. You can query GCS for 
objects in a bucket which start with a given Object Name prefix. 

A common character to include in file names is a slash (/). By using slashes in an object name, you 
can make objects appear as though they're stored in a hierarchical structure. For example, you 
could name one object `/europe/france/paris.jpg `and another object `/europe/france/cannes.jpg`, 
and querying GCS for files starting with  `/europe/france/` would give both 
`/europe/france/cannes.jpg` and `europe/france/paris.jpg`.


## Object Immutability [[4]](#references)

Objects are immutable and cannot change throughout its storage lifetime. Objects cannot be appended 
or truncated. However it is possible to overwrite objects in GCS, and doing so happens atomically. 
Until the new upload completes, the old version of the object is served to readers, and after the 
upload operation completes the new version of the object will be served.

Objects can be overwritten at about once per second.


## Object Change Notification [[6]](#references)

GCS can send a WebHook or launch a Cloud Function when an object is added, updated (overwritten), 
or removed. For an overview of how the flow works refer to the following diagram.


{{< image src="/images/cloud_storage/object_change_notification.svg" width="805" height="216" alt="Picture of object Change Notification" >}}


## Object Versioning [[7]](#references)

Cloud Storage allows you to enable Object Versioning at the bucket level. Once enabled, a history 
of modifications (overwrite / delete) of objects is kept for all objects in the bucket. You can 
list archived versions of an object, restore an object to an older state, or permanently delete a 
version, as needed.


## Object Lifecycle Management [[8]](#references)

To support common use cases like setting a Time to Live (TTL) for objects, archiving older versions 
of objects, or "downgrading" storage classes of objects to help manage costs, Google Cloud Storage 
offers the Object Lifecycle Management feature.

Lifecycle management configuration contains a set of rules which apply to all objects in a given 
bucket. Each rule in the set can contain multiple conditions. When an object meets all of the 
conditions of one of the rules, GCS automatically performs a specified action.

 Some example use cases are:

*   Downgrading the storage class of objects older than 365 days to coldine storage
*   Deleting objects created before January 1st, 2016
*   Keeping only the 3 most recent version of each object in a bucket that has versioning

For more information on object lifecycle management, and for a list of lifecycle management actions 
& conditions please visit the [Object Lifecycle Management 
Documentation](https://cloud.google.com/storage/docs/lifecycle).


# Storage Classes [[1]](#references)[[4]](#references)

A storage class specifies how many locations the data is made available. All storage classes share 
the same durability (chance of google loosing your data) of 99.999999999%, but have different 
availability (chance of being able to retrieve data).

Pricing information can be found in the [Cloud Storage Pricing 
Document](https://cloud.google.com/storage/pricing#storage-pricing).


## Multi-Regional [[5]](#references)

Multi-Regional buckets are appropriate for storing data that is frequently accessed. Some examples 
would be website content, interactive workloads, or data supporting mobile and gaming applications. 
Multi-Regional is the most available out of all the storage classes.

Multi-Regional is geo-redundant, and data is stored in at least two regions separated by at least 
100 miles. 


## Regional [[5]](#references)

Regional buckets are appropriate for storing data that is used by GCE instances. Doing so gives you 
better performance for data-intensive computations, as opposed to storing your data in a 
multi-regional location. In addition storing data as Regional can reduce network charges.

Regional storage is cheaper than multi-regional but less available as data is only stored in a 
specific regional location.


## Nearline [[5]](#references)

Nearline buckets are appropriate for storing data that is used on average once a month or less. For 
example if you want to continuously add files to GCS and plan to access those files once a month 
for analysis. 

Nearline storage is a low-cost, highly durable storage service for storing infrequently accessed 
data. Unlike Multi-Regional and Regional, Nearline storage comes with a 30-day minimum storage 
duration and has a data retrieval cost. 


## Coldline [[5]](#references)

Coldline buckets are appropriate for data that is used at most once a year. Some example use cases 
are data archiving, online backup, and disaster recovery. Unlike other "cold" storage services, 
your data is available within milliseconds , not hours or days.

Unlike Multi-Regional and Regional, Coldline Storage comes with a 90-day minimum storage duration 
and has a data retrieval cost. Coldine is cheaper than Nearline for storing data, but comes with a 
higher per operation cost and a lower availability.


## Comparison of Storage Classes [[3]](#references)

{{< table bordered striped >}}
<table>
<thead>
<tr>
  <th>Storage Class</th>
  <th>Characteristics</th>
  <th>Use Cases</th>
</tr>
</thead>
<tbody>
<tr>
  <td align="center">
    <a href="Multi-Regional](https://cloud.google.com/storage/docs/storage-classes#multi-regional">
      Multi Regional
    </a>
  </td>
  <td style="min-width:16em;">
    <ul>
      <li>99.95% Availability</li>
      <li>Geo-redundant</li>
    </ul>
  </td>
  <td style="min-width:16em">Storing data that is frequently accessed ("hot" objects) around the
    world, such as serving website content, streaming videos, or gaming and mobile applications.
  </td>
</tr>
<tr>
  <td align="center">
    <a href="https://cloud.google.com/storage/docs/storage-classes#regional">Regional Storage</a>
  </td>
  <td style="min-width:16em;">
    <ul>
      <li>99.9% availability</li>
      <li>Lower cost per GB stored</li>
      <li>Data stored in a narrow geographic region</li>
    </ul>
  </td>
  <td style="min-width:16em;">Storing frequently accessed in the same region as your Google Cloud
    DataProc or Google Compute Engine instances that use it, such as for data analytics.
  </td>
</tr>
<tr>
  <td align="center">
    <a href="https://cloud.google.com/storage/docs/storage-classes#nearline">
      Nearline Storage
    </a>
  </td>
  <td style="min-width:16em;">
    <ul>
      <li>99.0% availability</li>
      <li>Very low cost per GB stored</li>
      <li>Data retrieval costs</li>
      <li>Higher per-operation costs</li>
      <li>30-day minimum storage duration</li>
    </ul>
  </td>
  <td style="min-width:16em;">Data you do not expect to access frequently (i.e., no more than once 
    per month). Ideal for back-up and serving long-tail multimedia content.
  </td>
</tr>
<tr>
  <td align="center">
    <a href="https://cloud.google.com/storage/docs/storage-classes#coldline">
      Coldline Storage
    </a>
  </td>
  <td style="min-width:16em;">
    <ul>
      <li>99.0% availability</li>
      <li>Lowest cost per GB stored</li>
      <li>Data retrieval costs</li>
      <li>Higher per-operation costs</li>
      <li>90-day minimum storage duration</li>
    </ul>
  </td>
  <td style="min-width:16em;">Data you expect to access infrequently (i.e., no more than once per year).
    Typically this is for disaster recovery, or data that is archived and may or may not be needed at some future time.
  </td>
</tr>
</tbody>
</table>

{{</ table >}}

# Access Control [[9]](#references)

You can control who has access to GCS buckets and objects as well as what level of access they have 
through IAM, ACL, Signed URLs, and Signed Policy Documents.


## Identity and Access Management

The Google Cloud IAM system allows administrators to grant access to all of a projects buckets and 
objects. IAM permissions give broad control over your projects, but not fine-grained control over 
each individual bucket or object.


## Access Control Lists (ACLs)

ACLs can grant read or write access to an individual bucket or object. In many cases you can use 
IAM permissions instead of ACLs, and you should only use ACLs when needing fine-grained control 
over individual resources.


## Signed URLs (query string authentication)

Signed URLs give time-limited read or write access to an object. These URLs are generated through 
api or gsutil calls, and require a length of time the url is valid for. Anyone in the world with 
whom you share the URL can access the object, until the specified duration is over.


## Signed Policy Documents

Signed Policy Documents specify what can be uploaded to a bucket. Policy documents give greater 
control over size, content type, and other upload characteristics than signed URLs, and can be used 
by website owners to allow visitors to directly upload files to GCS.


# Labs {#labs}

[Upload Objects to Cloud 
Storage](https://codelabs.developers.google.com/codelabs/cloud-upload-objects-to-cloud-storage/index
.html?index=..%2F..%2Findex#0)


# Related Technologies {#related-technologies}

Amazon S3

Amazon Elastic File System (EFS)

Azure Blob Storage

Azure File Storage


# References {#references}

[1] https://cloud.google.com/storage/ 

[2] http://www.usatoday.com/story/tech/2013/10/18/asteroid-earth-2032/3007757/ 

[3] https://cloud.google.com/storage/docs/storage-classes 

[4] https://cloud.google.com/storage/docs/key-terms 

[5] https://cloud.google.com/storage/docs/storage-classes 

[6] https://cloud.google.com/storage/docs/object-change-notification 

[7] https://cloud.google.com/storage/docs/object-versioning 

[8] https://cloud.google.com/storage/docs/lifecycle 

[9] https://cloud.google.com/storage/docs/access-control/ 


