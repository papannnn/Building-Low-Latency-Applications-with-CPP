# Introducing Low Latency Application Development in C++

## Understanding requirements for latency-sensitive applications

**Latency** is time delay between task started to task finished.

That means, if request comes at T2, and it's finished at T10, the latency of that request is 8.

Low latency applications are applications that execute tasks and respond or return results as quickly as
possible.

Depending on context, some application are okay if the latency are higher, but some application also can't accept and will make the application useless when the latency are some microseconds higher.

For example, people are still okay if the website load are taking 100ms longer than usual.

But in other case, when Trading Engine are slower for some extra microseconds, it will may make the Trading Engine not profitable.

## Understanding latency-sensitive vs latency-critical application

**Latency Sensitive application** means, when the performance latency reduced, it will improve the business impact of that application.

That means it's still functional when the latency are higher, but can help the business impact when it goes lower.

Example app: OS, Web Browser, Databases, etc.

**Latency Critical application** means, when the performance latency are reached certain threshold, the application will be useless.

Example app: Autonomous Vehicles, Trading System, Traffic Control System, etc.

## Measuring Latency

There's some different method how to measure latency.

First thing to do, we need to think what is considered of beginning of processing task, and what is considered task being finished.

Another approach is what unit we need to measure, are we gonna use time as measurement unit? are we gonna use CPU clock cycle to measure something?

Here's some of the measurement we can use.

### Time to first byte

Measured when sender want to send data to the moment receiver get the first byte of that data.

Usually it's measures the network latency, but not always.

### Round Trip Time

Total time from sending the request, processing, until it got the reply.

```
client t0 ──request──►  t1 server
                         │ (processing)
client t3 ◄──reply────  t2 server
```

```
total RTT    = t3 − t0
network RTT  = (t3 − t0) − (t2 − t1)
```

### Tick-to-trade

Commonly used on electronic trading system.

Measure at when packet (market data packet) first hit the Trading System, to the time when Trading System process the packet, until the Trading System send order request to Exchange.

```
exchange ──tick──► T0 NIC rx ─► feed handler ─► strategy
                                                   ▼
exchange ◄─order── T1 NIC tx ◄─ order encode ◄─ risk check

tick-to-trade = T1 − T0   (both measured at the wire)
```

### CPU clock cycles

Measuring CPU clock usually measuring it in instruction level. How much spin the CPU clock will do for that kind of instruction.

## Differentiating between latency metrics

### Throughput vs Latency

**Throughput** is how much work you can done in certain period of time.

**Latency** is how long that single work took.

To improve **throughput**, introduce parallelism, add more worker, etc, the speed of 1 work is not gonna improved, but the amount of work finished will gonna increased.

## Requirements of latency-sensitive applications

### Correctness and Robustness

Being fast is not enough, but it should be correct. Imagine if the trading system track the wrong price of stock, it would affect the profitability.

It should be robust, being robust means the system will not easily go down when something bad happen.

### Capped peak latency and Predictable latency

Peak latency is large outlier latency that happen in our system.

It's better to have a task that has average latency of 20 microseconds / task, rather than average latency of 5 microseconds / task but it has a chance the latency go up into 40 microseconds at some random time.

Imagine if that's happen when we want to sell stock, but because that sudden spike, we failed to sell our stock because the price already changed.

We want to make sure the peak latency we have to be as minimum as we can, therefore our system will have high predictability.

## Understanding why C++ is the preferred programming language

### Compiled Language

C++ is **Compiled Language**

**Compiled Language** is a language where it's source code need to be converted into machine understandable code first before it can be run.

**Intrepreted Language** is a language where it's source code can be run by another application called intrepreter.

Because it need another layer, **Intrepreted Language** runs slower than **Compiled Language**.

### Closer to Hardware

C++ language has low level support.

For example, it can manages it's own memory and use pointer to point actual address in computer memory.

Because C++ language has less abstraction that other programming language, it makes C++ language more faster than the other.
