---
description: Review best practices to deal with 5XX errors
---

# 5XX Retry Logic Best Practices

## Introduction

Since DataHub is a proxy that passes through errors from full node software, our users will need to adopt defensive strategies for interacting with the API.  
  
We are making it easy for our users to do the right thing and providing examples of pseudo-code to copy/paste.

  
For Example:

```ruby
tries = 3
begin
  r = RestClient.get(...)
rescue
  tries -= 1
  retry if tries > 0
end
```

## **1. Best Practices to Add Retries to API Calls in NodeJS**

### **The basics of a retry**

To decide when to retry a request, we need to consider what to look for. There are a handful of HTTP status codes that you can check against. This will let your retry logic differentiate between a failed request that is appropriate to retry—like a gateway error—and one that isn't—like a 404. In our examples, we will use **408, 500, 502, 503, 504, 522, and 524.**

The next consideration we want is how often to retry. We will start with a delay, then increase it each additional time. This is a concept known as "back-off". The time between requests will grow with each attempt. Finally, we'll also need to decide how many attempts to make before giving up.

**Here's an example of the logic we'll be using in pseudo-code:**

* If total attempts &gt; attempts, continue
* if status code type matches, continue
* if \(now - delay\) &gt; last attempt, try request
* else, return to the start

### **Recursive request structure**

To make this all work, we'll be making a request from within a failed request. This requires the use of recursion. Recursion is when a function calls itself.

For example, if we wanted to infinitely keep trying to make a request it might look like this:

```ruby
function myRequest(url, options = {}) {
  return requests(url, options, response => {
    if (response.ok) {
      return response
    } else {
      return myRequest(url, options)
    }
  })
}
```

The `else` block returns the `myRequest` function. Since most modern HTTP request implementations are promise-based, we can return the result.

### Add retry to Fetch

First, start with the browser's Fetch API. The fetch implementation will be similar to the recursion example above. Let's implement that same example, but using fetch and a status check.

```ruby
function fetchRetry(url, options) {
  // Return a fetch request
  return fetch(url, options).then(res => {
    // check if successful. If so, return the response transformed to json
    if (res.ok) return res.json()
    // else, return a call to fetchRetry
    return fetchRetry(url, options)
  })
}
```

This will work to infinitely retry failed requests. Note: a `return` will break out of the current block, so we don't need an else statement after `return res.json()` .

### **For setting up max number of retries**

```ruby
function fetchRetry(url, options = {}, retries = 3) {
  return fetch(url, options)
    .then(res => {
      if (res.ok) return res.json()

      if (retries > 0) {
        return fetchRetry(url, options, retries - 1)
      } else {
        throw new Error(res)
      }
    })
    .catch(console.error)
}
```

Add the `retries` argument to the function, with a default value of 3. Then, rather than automatically calling the function on failure, check if any `retries` are remaining. If so, call `fetchRetry`. The new retries value passed to the next attempt is the current `retries` minus 1. This ensures that our "loop" decrements, and eventually will stop. Without this, it would run infinitely until the request succeeds. Finally, if `retries` are not greater than zero, throw a new error for `.catch` to handle.

### **The incremental back-off delay between each request**

```ruby
function fetchRetry(url, options = {}, retries = 3, backoff = 300) {
  /* 1 */
  const retryCodes = [408, 500, 502, 503, 504, 522, 524]
  return fetch(url, options)
    .then(res => {
      if (res.ok) return res.json()

      if (retries > 0 && retryCodes.includes(res.status)) {
        setTimeout(() => {
          /* 2 */
          return fetchRetry(url, options, retries - 1, backoff * 2) /* 3 */
        }, backoff) /* 2 */
      } else 
      {
        throw new Error(res)
      }
    })
    .catch(console.error)
}
```

To handle the "wait" mechanic before retrying the request, you can use `setTimeout`. First, we add our new configuration argument \(1\). Then, set up the `setTimeout` and use the `backoff` value as the delay. Finally, when the retry occurs we also pass in the back-off with a modifier. In this case, `backoff * 2`. This means each new retry will wait twice as long as the previous.

### **Add retry to Node's native http module**

You could use a fetch-equivalent library like node-fetch. To make things interesting, let's look at applying the same concepts above to Node.js' native http module.

Here's a basic GET using http.get: ****

```ruby
let https = require("https")

https.get(url, res => {
  let data = ""
  let { statusCode } = res

  if (statusCode < 200 || statusCode > 299) {
    throw new Error(res)
  } else {
    res.on("data", d => {
      data += d
    })
    res.end("end", () => {
      console.log(data)
    })
  }
})
```

To summarize, it requests a url. If the statusCode isn't in a defined "success range" \(Fetch has the ok property to handle this\) it throws an error. Otherwise, it builds a response and logs to the console. Let's look at what this looks like "promisified". To make it easier to follow, we'll leave off some of the additional error handling.

```ruby
function retryGet(url) {
  return new Promise((resolve, reject) => {
    https.get(url, res => {
      let data = ""
      const { statusCode } = res
      if (statusCode < 200 || statusCode > 299) {
        reject(Error(res))
      } else {
        res.on("data", d => {
          data += d
        })
        res.on("end", () => {
          resolve(data)
        })
      }
    })
  })
}
```

### **Final code to retryGet**

```ruby
function retryGet(url, retries = 3, backoff = 300) {
  /*  1 */
  const retryCodes = [408, 500, 502, 503, 504, 522, 524] /* 2 */
  return new Promise((resolve, reject) => {
    https.get(url, res => {
      let data = ""
      const { statusCode } = res
      if (statusCode < 200 || statusCode > 299) {
        if (retries > 0 && retryCodes.includes(statusCode)) {
          /* 3 */
          setTimeout(() => {
            return retryGet(url, retries - 1, backoff * 2)
          }, backoff)
        } else {
          reject(Error(res))
        }
      } else {
        res.on("data", d => {
          data += d
        })
        res.on("end", () => {
          resolve(data)
        })
      }
    })
  })
}
```

Source & for more details - [Check Here](https://hackernoon.com/how-to-improve-your-backend-by-adding-retries-to-your-api-calls-83r3udx)

## **NodeJS - Re-Calling function on error callback pseudo code**

```ruby
function requestRetry(url, data, retryTimes, retryDelay, callback) {
    var cntr = 0;

    function run() {
        // try your async operation
        request(..., function(err, data) {
            ++cntr;
            if (err) {
                if (cntr >= retryTimes) {
                    // if it fails too many times, just send the error out
                    callback(err);
                } else {
                    // try again after a delay
                    setTimeout(run, retryDelay);
                }
            } else {
                // success, send the data out
                callback(null, data);
            }
        });
    }
    // start our first request
    run();
}


requestRetry(someUrl, someData, 10, 500, function(err, data) {
    if (err) {
        // still failed after 10 retries
    } else {
        // got successful result here
    }
});
```

This is a fairly simple retry scheme, it just retries on a fixed interval for a fixed number of times. More complicated schemes implement a back-off algorithm where they start with fairly quick retries, but then back-off to a longer period of time between retries after the first few failures to give the server a better chance of recovering. 

If there happens to be lots and lots of clients all doing rapid retries, then as soon as your server has a hiccup, you can get an avalanche failure as all the clients suddenly start rapidly retrying which just puts your server in even more trouble trying to handle all those requests. The back-off algorithm is designed to allow a server a better chance of preventing an avalanche failure and make it easier for it to recover.

The back-off scheme is also more appropriate if you're waiting for the service to come back online after it's been down a little while.

**Also, Here is an asynchronous loop for retrying a specific number of times:**

```ruby
function asyncLoop(i, range, callback) {
    var results = 0;
    if(i < range) {
        // do something, update results
        aysncLoop(i+1, range, callback); 
    } else {
        callback(null, results)
    }
}
```

  
To loop it 10 times, call it as follows: 

```ruby
asyncLoop(0, 10, function(err, results) {
    console.log(results);
});
```

Pass your error condition in place of range and check it inside the loop.

