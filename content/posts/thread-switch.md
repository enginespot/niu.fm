+++
author = "小牛快跑"
title = "多线程消息传递示例"
date = "2022-11-25"
description = "演示"
tags = [
    "rust",
]
+++

```rust {linenos=table}
use std::{sync::{Arc, Condvar, Mutex},
          time::Instant};

#[derive(Debug)]

struct Mydata(u128, u128);

fn main() {

    let pair = Arc::new((Mutex::new(None), Condvar::new()));

    let pair2 = Arc::clone(&pair);

    let start = Instant::now();

    let hdl = std::thread::spawn(move || {

        // let (lock, cvar) = &*pair2;

        let mut counter = 0;

        let mut guard = pair2.0.lock().unwrap();

        loop {

            guard = pair2
                .1
                .wait_while(guard, |pending| pending.is_some())
                .unwrap();

            counter += 1;

            let duration = start.elapsed();

            if counter % 1000000 == 0{

                println!(
                    "inner counter: {},{}",
                    counter,
                    duration.as_nanos() / counter
                );
            }

            *guard = Some(Mydata(counter, counter));

            pair2.1.notify_one()
        }
    });

    let (lock, cvar) = &*pair;

    let mut counter = 0;

    let mut guard = lock.lock().unwrap();

    loop {

        counter += 1;

        let duration = start.elapsed();

        if counter % 10000 == 0 {

            println!(
                "outside counter: {:?},{}",
                *guard,
                duration.as_nanos() / counter
            );
        }

        *guard = None;

        cvar.notify_one();

        guard = cvar.wait_while(guard, |pending| pending.is_none()).unwrap();
    }

    hdl.join().unwrap();
}
```