# Oraq

## Description

Oraq (Ordered redis asynchronous queue) is a cross process, cross server, fast, reliable and lightweight redis-based async queue for Node.

## Features

- super fast
- cross process
- easy to use

## Compatibility

Supports node.js >= 7.6.0.

## Installation

```bash
npm install --save oraq
```

Add 'notify-keyspace-events' in redis.conf 
notify-keyspace-events="Kgxl"

## Usage

```js
const Oraq = require('oraq');
const oraq = new Oraq({
  id: 'myQueue',
  concurrency: 1
});

const job = ms => new Promise(resolve => setTimeout(() => resolve(ms), ms));

// do a lot of heavy jobs with concurrency 1
Promise.all([
  14000,
  8000,
  10000,
  7000
].map(delay => oraq.limit(job, {jobData: delay})))
  .then(jobResults => console.log(jobResults));
```

## Constructor options

```js
const oraq = new Oraq({
  id,                        // id {string} (limiters with the same prefix and id share their queues, "queue" by default)
  prefix,                    // custom redis key prefix {string} ("oraq" by default)
  connection,                // redis connection param {any} (anything that ioredis constructor supports)
  ping,                      // processing job keep alive interval in ms {integer} (60000 by default)
  timeout,                   // job will run after this time {integer} (in case of too long previous tasks processing, 2 * 60 * 60 * 1000 (2 hours) by default)
  concurrency,               // jobs concurrency {integer} (1 by default)
  mode                       // mode {string} ("limiter" - rate limiter (no order guarantee) or "queue" - real queue (keep order), "queue" by default)
});
```

## API

```js
const oraq = new Oraq();

oraq.limit(
  job,                       // job {function}
  jobOptions = {             // job options {object}
    jobId,                   // job id {string} (random string by default)
    jobData,                 // data to pass as an argument to the job {any}
    lifo                     // last-in-first-out {boolean} (false by default)
  }
).then(jobResult => console.log(jobResult));

oraq.quit()                  // same as ioredis quit method (used to close redis connection)
  .then(() => console.log('Redis connection is closed'));

oraq.removeJobById('jobId')  // remove job from pending queue
  .then(() => console.log('Job is removed'));
```

## Tests

```bash
npm test
```

## License

MIT © [Yevhen Samoilenko](https://github.com/deugene)
