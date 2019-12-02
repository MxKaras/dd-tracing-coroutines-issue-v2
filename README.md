# dd-tracing-coroutines-issue-v2
Sample project to demonstrate issues with coroutines when using dd-java-agent 0.37.0

## Application description
There is a loop in which async coroutines are created. Newly created coroutine is waiting for result of the previously created one. It's causing issues with dd agent. See logs below:

 ```
> Task :run
[dd.trace 2019-12-02 09:09:57:488 +0100] [main] INFO datadog.trace.agent.jmxfetch.JMXFetch - JMXFetch config: null [] [] [] null null {runtime-id=357611cc-cfd8-4ff6-b5cf-cf71799b5ad4, service=unnamed-java-app} statsd:localhost:8125 System.err INFO
[dd.trace 2019-12-02 09:09:58:141 +0100] [main] INFO datadog.trace.agent.ot.DDTraceOTInfo - dd-trace - version: 0.38.0~1cb704e7
[dd.trace 2019-12-02 09:09:58:208 +0100] [main] INFO datadog.trace.agent.ot.DDTracer - New instance: DDTracer-12d5c30e{ serviceName=unnamed-java-app, writer=DDAgentWriter { api=DDApi { tracesUrl=http://localhost:8126/v0.3/traces } }, sampler=datadog.trace.agent.common.sampling.RateByServiceSampler@b887730, defaultSpanTags={}}
[dd.trace 2019-12-02 09:09:58:233 +0100] [main] INFO datadog.trace.agent.tooling.VersionLogger - dd-trace-ot - version: 0.38.0~1cb704e7
[dd.trace 2019-12-02 09:09:58:233 +0100] [main] INFO datadog.trace.agent.tooling.VersionLogger - dd-trace-api - version: 0.38.0~1cb704e7
[dd.trace 2019-12-02 09:09:58:234 +0100] [main] INFO datadog.trace.agent.tooling.VersionLogger - dd-java-agent - version: 0.38.0~1cb704e7
2019-12-02 09:09:59.244  INFO 3011 [           main] Main$main$1                              : main DDSpan [ t_id=7193350600673630288, s_id=5072233434250293661, p_id=0] trace=unnamed-java-app/trace.annotation/Main.main metrics={} tags={component=trace, language=jvm, runtime-id=357611cc-cfd8-4ff6-b5cf-cf71799b5ad4, thread.id=1, thread.name=main}, duration_ns=0
2019-12-02 09:09:59.289  INFO 3011 [atcher-worker-2] Main                                     : DefaultDispatcher-worker-2 DDSpan [ t_id=7193350600673630288, s_id=746117545160333243, p_id=5072233434250293661] trace=unnamed-java-app/trace.annotation/Main.waitFor metrics={} tags={Iteration=0, component=trace, thread.id=26, thread.name=DefaultDispatcher-worker-2}, duration_ns=0
2019-12-02 09:09:59.290  INFO 3011 [atcher-worker-2] Main                                     : DefaultDispatcher-worker-2 DDSpan [ t_id=7193350600673630288, s_id=746117545160333243, p_id=5072233434250293661] trace=unnamed-java-app/trace.annotation/Main.waitFor metrics={} tags={Iteration=0, component=trace, thread.id=26, thread.name=DefaultDispatcher-worker-2}, duration_ns=0
2019-12-02 09:09:59.295  INFO 3011 [atcher-worker-3] Main                                     : DefaultDispatcher-worker-3 DDSpan [ t_id=7193350600673630288, s_id=7197528648774239774, p_id=5072233434250293661] trace=unnamed-java-app/trace.annotation/Main.waitFor metrics={} tags={Iteration=1, component=trace, thread.id=27, thread.name=DefaultDispatcher-worker-3}, duration_ns=0
2019-12-02 09:09:59.296  INFO 3011 [atcher-worker-1] Main                                     : DefaultDispatcher-worker-1 DDSpan [ t_id=7193350600673630288, s_id=8066797619210739209, p_id=5072233434250293661] trace=unnamed-java-app/trace.annotation/Main.waitFor metrics={} tags={Iteration=2, component=trace, thread.id=25, thread.name=DefaultDispatcher-worker-1}, duration_ns=0
2019-12-02 09:09:59.296  INFO 3011 [atcher-worker-3] Main                                     : DefaultDispatcher-worker-3 DDSpan [ t_id=7193350600673630288, s_id=7197528648774239774, p_id=5072233434250293661] trace=unnamed-java-app/trace.annotation/Main.waitFor metrics={} tags={Iteration=1, component=trace, thread.id=27, thread.name=DefaultDispatcher-worker-3}, duration_ns=0
2019-12-02 09:09:59.298  INFO 3011 [atcher-worker-4] Main                                     : DefaultDispatcher-worker-4 DDSpan [ t_id=7193350600673630288, s_id=4296975517628872484, p_id=5072233434250293661] trace=unnamed-java-app/trace.annotation/Main.waitFor metrics={} tags={Iteration=3, component=trace, thread.id=28, thread.name=DefaultDispatcher-worker-4}, duration_ns=0
2019-12-02 09:09:59.298  INFO 3011 [atcher-worker-6] Main                                     : DefaultDispatcher-worker-6 DDSpan [ t_id=7193350600673630288, s_id=5365827836112656210, p_id=5072233434250293661] trace=unnamed-java-app/trace.annotation/Main.waitFor metrics={} tags={Iteration=6, component=trace, thread.id=30, thread.name=DefaultDispatcher-worker-6}, duration_ns=0
2019-12-02 09:09:59.310  INFO 3011 [tcher-worker-11] Main                                     : DefaultDispatcher-worker-11 DDSpan [ t_id=7193350600673630288, s_id=7628790482180232586, p_id=5072233434250293661] trace=unnamed-java-app/trace.annotation/Main.waitFor metrics={} tags={Iteration=7, component=trace, thread.id=35, thread.name=DefaultDispatcher-worker-11}, duration_ns=0
2019-12-02 09:09:59.311  INFO 3011 [tcher-worker-16] Main                                     : DefaultDispatcher-worker-16 DDSpan [ t_id=7193350600673630288, s_id=5183884234796495144, p_id=5072233434250293661] trace=unnamed-java-app/trace.annotation/Main.waitFor metrics={} tags={Iteration=9, component=trace, thread.id=40, thread.name=DefaultDispatcher-worker-16}, duration_ns=0
2019-12-02 09:09:59.301  INFO 3011 [atcher-worker-5] Main                                     : DefaultDispatcher-worker-5 DDSpan [ t_id=7193350600673630288, s_id=6433263087237591893, p_id=5072233434250293661] trace=unnamed-java-app/trace.annotation/Main.waitFor metrics={} tags={Iteration=4, component=trace, thread.id=29, thread.name=DefaultDispatcher-worker-5}, duration_ns=0
2019-12-02 09:09:59.300  INFO 3011 [atcher-worker-7] Main                                     : DefaultDispatcher-worker-7 DDSpan [ t_id=7193350600673630288, s_id=4265838820205176214, p_id=5072233434250293661] trace=unnamed-java-app/trace.annotation/Main.waitFor metrics={} tags={Iteration=5, component=trace, thread.id=31, thread.name=DefaultDispatcher-worker-7}, duration_ns=0
2019-12-02 09:09:59.312  INFO 3011 [tcher-worker-10] Main                                     : DefaultDispatcher-worker-10 DDSpan [ t_id=7193350600673630288, s_id=548470849145633943, p_id=5072233434250293661] trace=unnamed-java-app/trace.annotation/Main.waitFor metrics={} tags={Iteration=8, component=trace, thread.id=34, thread.name=DefaultDispatcher-worker-10}, duration_ns=0
2019-12-02 09:10:00.348  INFO 3011 [atcher-worker-3] Main                                     : DefaultDispatcher-worker-3 DDSpan [ t_id=2885891384756554864, s_id=1354707583510125025, p_id=0] trace=unnamed-java-app/trace.annotation/Main.waitFor metrics={} tags={component=trace, language=jvm, runtime-id=357611cc-cfd8-4ff6-b5cf-cf71799b5ad4, thread.id=27, thread.name=DefaultDispatcher-worker-3}, duration_ns=0
2019-12-02 09:10:00.348  INFO 3011 [atcher-worker-2] Main                                     : DefaultDispatcher-worker-2 DDSpan [ t_id=7056549324730239564, s_id=763352010943057162, p_id=0] trace=unnamed-java-app/trace.annotation/Main.waitFor metrics={} tags={component=trace, language=jvm, runtime-id=357611cc-cfd8-4ff6-b5cf-cf71799b5ad4, thread.id=26, thread.name=DefaultDispatcher-worker-2}, duration_ns=0
[dd.trace 2019-12-02 09:10:01:226 +0100] [dd-trace-writer] WARN datadog.trace.agent.common.writer.DDApi - Error while sending 4 of 8 traces to the DD agent. java.net.ConnectException: Failed to connect to localhost/0:0:0:0:0:0:0:1:8126 (going silent for 5 minutes)
2019-12-02 09:10:01.352  INFO 3011 [atcher-worker-2] Main                                     : DefaultDispatcher-worker-2 DDSpan [ t_id=5320033692214077167, s_id=1855406219590974488, p_id=0] trace=unnamed-java-app/trace.annotation/Main.waitFor metrics={} tags={component=trace, language=jvm, runtime-id=357611cc-cfd8-4ff6-b5cf-cf71799b5ad4, thread.id=26, thread.name=DefaultDispatcher-worker-2}, duration_ns=0
2019-12-02 09:10:01.352  INFO 3011 [atcher-worker-4] Main                                     : DefaultDispatcher-worker-4 DDSpan [ t_id=8606263219805753497, s_id=8467046378833814605, p_id=0] trace=unnamed-java-app/trace.annotation/Main.waitFor metrics={} tags={component=trace, language=jvm, runtime-id=357611cc-cfd8-4ff6-b5cf-cf71799b5ad4, thread.id=28, thread.name=DefaultDispatcher-worker-4}, duration_ns=0
2019-12-02 09:10:02.357  INFO 3011 [tcher-worker-10] Main                                     : DefaultDispatcher-worker-10 DDSpan [ t_id=2365551920306348646, s_id=713191865639339465, p_id=0] trace=unnamed-java-app/trace.annotation/Main.waitFor metrics={} tags={component=trace, language=jvm, runtime-id=357611cc-cfd8-4ff6-b5cf-cf71799b5ad4, thread.id=34, thread.name=DefaultDispatcher-worker-10}, duration_ns=0
2019-12-02 09:10:02.357  INFO 3011 [tcher-worker-16] Main                                     : DefaultDispatcher-worker-16 DDSpan [ t_id=867930530800665266, s_id=5882884266206398300, p_id=0] trace=unnamed-java-app/trace.annotation/Main.waitFor metrics={} tags={component=trace, language=jvm, runtime-id=357611cc-cfd8-4ff6-b5cf-cf71799b5ad4, thread.id=40, thread.name=DefaultDispatcher-worker-16}, duration_ns=0
2019-12-02 09:10:03.365  INFO 3011 [tcher-worker-10] Main                                     : DefaultDispatcher-worker-10 DDSpan [ t_id=6205017500113667951, s_id=6683084317200385240, p_id=0] trace=unnamed-java-app/trace.annotation/Main.waitFor metrics={} tags={component=trace, language=jvm, runtime-id=357611cc-cfd8-4ff6-b5cf-cf71799b5ad4, thread.id=34, thread.name=DefaultDispatcher-worker-10}, duration_ns=0
2019-12-02 09:10:03.365  INFO 3011 [tcher-worker-16] Main                                     : DefaultDispatcher-worker-16 DDSpan [ t_id=7869260088474755294, s_id=8336849268365864, p_id=0] trace=unnamed-java-app/trace.annotation/Main.waitFor metrics={} tags={component=trace, language=jvm, runtime-id=357611cc-cfd8-4ff6-b5cf-cf71799b5ad4, thread.id=40, thread.name=DefaultDispatcher-worker-16}, duration_ns=0
```

As you can see only traces with iteration 0 and 1 has correct info. All the other coroutines has their's spans info lost and recreated as if it was parent span.

## How to start?

`./gradlew run`