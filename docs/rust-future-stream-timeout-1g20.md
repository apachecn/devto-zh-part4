# 信任未来(流)超时

> 原文：<https://dev.to/x1957/rust-future-stream-timeout-1g20>

Open tokio-timer to see timout 【T0].

```
#[must_use = "futures do nothing unless polled"]
#[derive(Debug)]
pub struct Timeout<T> {
    value: T,
    delay: Delay,
} 
```

Very simple structure, value is the future or stream that we want to add timeout, and delay is how long before timeout (:

Look at the Future and Stream trait 【T0] that he realized.

```
impl<T> Future for Timeout<T>
where
    T: Future,
{
    type Item = T::Item;
    type Error = Error<T::Error>;

    fn poll(&mut self) -> Poll<Self::Item, Self::Error> {
        // First, try polling the future
        match self.value.poll() {
            Ok(Async::Ready(v)) => return Ok(Async::Ready(v)),
            Ok(Async::NotReady) => {}
            Err(e) => return Err(Error::inner(e)),
        }

        // Now check the timer
        match self.delay.poll() {
            Ok(Async::NotReady) => Ok(Async::NotReady),
            Ok(Async::Ready(_)) => Err(Error::elapsed()),
            Err(e) => Err(Error::timer(e)),
        }
    }
}

impl<T> Stream for Timeout<T>
where
    T: Stream,
{
    type Item = T::Item;
    type Error = Error<T::Error>;

    fn poll(&mut self) -> Poll<Option<Self::Item>, Self::Error> {
        // First, try polling the future
        match self.value.poll() {
            Ok(Async::Ready(v)) => {
                if v.is_some() {
                    self.delay.reset_timeout();
                }
                return Ok(Async::Ready(v));
            }
            Ok(Async::NotReady) => {}
            Err(e) => return Err(Error::inner(e)),
        }

        // Now check the timer
        match self.delay.poll() {
            Ok(Async::NotReady) => Ok(Async::NotReady),
            Ok(Async::Ready(_)) => {
                self.delay.reset_timeout();
                Err(Error::elapsed())
            }
            Err(e) => Err(Error::timer(e)),
        }
    } 
```

It's normal to go to poll value. If it's ready, go back directly. If it's notready, go to poll dealy again to see if it's timed out. If it's not timed out, it's returned to Not Ready. If it's timed out, it's returned to Timelapsed Error.

Very simple logic implements timeout (:

If I want to use timeout conveniently, I may need to use tokio::prelude::FutureExt;

```
fn timeout(self, timeout: Duration) -> Timeout<Self>
    where
        Self: Sized,
    {
        Timeout::new(self, timeout)
    } 
```

Actually, you can use Timeout::new yourself, but xxx.timeout looks more comfortable.