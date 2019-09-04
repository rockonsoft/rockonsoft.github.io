---
layout: post
title: Firestore and Dates.
---

Google's Firestore is magical. You save stuff and it stays saved. Get the right observables in place and you will get your updated data, without having to query for it.

Working with Dates in Firestore is a bit magical, too. From your Node code you just save a date, and it just works. The fun starts when you try to read that date back, and the results might be surprising. This blog focuses on using Timestamp for dates and not Date and Time.

## Why save a Timestamp

You could save dates as string or the unix timestamps instead of using Timestamp.
Some of the reasons I like using it is:

- Looking at the date in the Firestore console, you get a formatted date sting, making debugging and validation.
- It is Firestore's build-in Date-Time object and allows for queries and ordering documents by date.
- The conversion between Date and Timestamp makes it easy to work with using Javascript or TypeScript code.

The [code](https://github.com/rockonsoft/firestore_dates) for this blog is a Node utility written in TypeScript based of my [repo](https://github.com/rockonsoft/node_async_ts)

## Connecting to Firestore

Before you can examine the Timestamp objects, you need to connect to Firestore:

### Connecting to Firestore with Node

Create a key file for you service account:

- If you are using the Firebase console, go to |Project Settings| Service Accounts | Generate New Private Key. This should download a .json key file that you will use to authenticate with Firestore.
- If you are using the Google Cloud Platform console, go to IAM & admin | Service Accounts. Select the account you want to use and generate the key file with the Create Key inline option.

In the code, I use a command line option to specify the key file, consequently keeping it as far away from the code as possible.
The connection is done by instantiating a service account from the key file, and initializing the firestore admin libraries with it:

```js
const serviceAccount = JSON.parse(fs.readFileSync(keyfile).toString())
admin.initializeApp({
  credential: admin.credential.cert(serviceAccount)
})
```

## Using Firestore Timestamp to Save Dates
### Timestamp
The timestamp object is documented (here)[https://firebase.google.com/docs/reference/js/firebase.firestore.Timestamp]

It is a object with 2 properties, seconds and nanoseconds, the seconds being a UNIX timestamp and the nanoseconds providing additional precision.

### Date
The Javascript Date object is used for both date and time. The most vexing property when working with Date objects and Timestamps is that the Date object is localized to your brower's or node environment's locale.

### Moment
(MomentJS)[https://momentjs.com/docs/] is my goto date and time library. It has a great bunch of utility functions for manipulating dates and times.

### Creating Timestamps with Date and Moment

To illustrate saving timestamps to Firestore, I constructed a payload with date initialized slightly different and then examined the nuanced differences
```js
  const payload = {
    nowStr: moment().format(), //plain string
    plainJsDate: new Date(), //from date object
    momentToDate: moment().toDate(), //from date object via moment
    utcStartOfDate: moment().utc().startOf('date').toDate(), //moment UTC date
    utcStartOfDay: moment().utc().startOf('day').toDate(), //moment UTC date
    startOfDay: moment().startOf('day').toDate(), //moment start of day
    utc: moment().utc().toDate(), //moment UTC date
    unix: moment().unix(), //just the UNIX timestamp
  };
```
I also throw a UNIX timestamp and string values in there to compare.

Reading Timestamps
Reading the object back is straight forward, and when you examine the raw json you will see the seconds and nanosecond parts of the Timestamp:

```json
{"_seconds":1567576668,"_nanoseconds":156000000}
```

Timestamp has a 'toDate()' method that will return a javascript date object.
Using the toDate() method, I printed out all the saved dates:

```bash
plainJsDate: Raw:{"_seconds":1567576668,"_nanoseconds":155000000}     :2019-09-04T07:57:48+02:00
momentToDate: Raw:{"_seconds":1567576668,"_nanoseconds":155000000}    :2019-09-04T07:57:48+02:00
utcStartOfDate: Raw:{"_seconds":1567555200,"_nanoseconds":0} toDate() :2019-09-04T02:00:00+02:00
utcStartOfDay: Raw:{"_seconds":1567555200,"_nanoseconds":0} toDate()  :2019-09-04T02:00:00+02:00
startOfDay: Raw:{"_seconds":1567548000,"_nanoseconds":0} toDate()     :2019-09-04T00:00:00+02:00
utc: Raw:{"_seconds":1567576668,"_nanoseconds":156000000}             :2019-09-04T07:57:48+02:00
```
From the above you can see that:
```js
    new Date(), moment().toDate(),moment().utc().toDate()
```
all produced equivalent dates and times.
Similarly, 
```js
    moment().utc().startOf('date').toDate(), moment().utc().startOf('day').toDate(),: moment().startOf('day').toDate(), 
```
has produced similar looking dates, except when you examine the _seconds portion of the 'startOfDay' Timestamp, 
you will see that it numerically less that the other two. All three has the nanosecond parts set to 0;

For the next set of results I use the moment's unix function to access the _seconds part of the Timestamp directly:
```js
moment.unix(timestamp._seconds).format()
```
This produced output of:
```bash
plainJsDate: Raw :{"_seconds":1567576668,"_nanoseconds":155000000}  unix :2019-09-04T07:57:48+02:00
momentToDate: Raw :{"_seconds":1567576668,"_nanoseconds":155000000} unix :2019-09-04T07:57:48+02:00
utcStartOfDate: Raw :{"_seconds":1567555200,"_nanoseconds":0}       unix :2019-09-04T02:00:00+02:00
utcStartOfDay: Raw :{"_seconds":1567555200,"_nanoseconds":0}        unix :2019-09-04T02:00:00+02:00
startOfDay: Raw :{"_seconds":1567548000,"_nanoseconds":0}           unix :2019-09-04T00:00:00+02:00
utc: Raw :{"_seconds":1567576668,"_nanoseconds":156000000}          unix :2019-09-04T07:57:48+02:00
```
Again the startOfDay value looks the same, but has a different timezone value.

Finally, I used the moment's 'utc' method to convert all dates to UTC.
```js
moment.unix(timestamp._seconds).utc().format()
```

This produced:
```bash
plainJsDate:Raw :{"_seconds":1567576668,"_nanoseconds":155000000}  unix :2019-09-04T05:57:48Z
momentToDate:Raw :{"_seconds":1567576668,"_nanoseconds":155000000} unix :2019-09-04T05:57:48Z
utcStartOfDate:Raw :{"_seconds":1567555200,"_nanoseconds":0}       unix :2019-09-04T00:00:00Z
utcStartOfDay:Raw :{"_seconds":1567555200,"_nanoseconds":0}        unix :2019-09-04T00:00:00Z
startOfDay:Raw :{"_seconds":1567548000,"_nanoseconds":0}           unix :2019-09-03T22:00:00Z
utc:Raw :{"_seconds":1567576668,"_nanoseconds":156000000}          unix :2019-09-04T05:57:48Z
```
Now that the dates were converted to UTC, the numerical difference in the startOfDay and utcStartOfDay becomes apparent. The two dates started out referring to seemingly the same date, have not diverged and you have two different dates and a subtle bug.

## Conclusion
When you are only interested in the saving dates, without times to Firestore, you should:
* be aware that Date is locale dependent.
* Timestamp saves the UNIX timestamp in its _seconds property.
* Stick to UTC, and convert all dates to UTC.




