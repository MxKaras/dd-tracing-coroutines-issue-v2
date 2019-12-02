# dd-tracing-coroutines-issue-v2
Sample project to demonstrate issues with coroutines when using dd-java-agent 0.37.0

## Application description
There is a loop in which async coroutines are created. Newly created coroutine is waiting for result of the previously created one. It's causing issues with dd agent. See logs below:

 ```
> Task :run
[dd.trace 2019-12-02 09:23:39:910 +0100] [main] INFO datadog.trace.agent.jmxfetch.JMXFetch - JMXFetch config: null [] [] [] null null {runtime-id=fedd6bdd-2499-451c-bb36-0f662f3e8648, service=unnamed-java-app} statsd:localhost:8125 System.err INFO
[dd.trace 2019-12-02 09:23:40:502 +0100] [main] INFO datadog.trace.agent.ot.DDTraceOTInfo - dd-trace - version: 0.38.0~1cb704e7
[dd.trace 2019-12-02 09:23:40:571 +0100] [main] INFO datadog.trace.agent.ot.DDTracer - New instance: DDTracer-77bb48d5{ serviceName=unnamed-java-app, writer=DDAgentWriter { api=DDApi { tracesUrl=http://localhost:8126/v0.3/traces } }, sampler=datadog.trace.agent.common.sampling.RateByServiceSampler@181d8899, defaultSpanTags={}}
[dd.trace 2019-12-02 09:23:40:600 +0100] [main] INFO datadog.trace.agent.tooling.VersionLogger - dd-trace-ot - version: 0.38.0~1cb704e7
[dd.trace 2019-12-02 09:23:40:600 +0100] [main] INFO datadog.trace.agent.tooling.VersionLogger - dd-trace-api - version: 0.38.0~1cb704e7
[dd.trace 2019-12-02 09:23:40:601 +0100] [main] INFO datadog.trace.agent.tooling.VersionLogger - dd-java-agent - version: 0.38.0~1cb704e7
2019-12-02 09:23:41.840  INFO 3998 [           main] Main$main$1                              : main DDSpan [ t_id=2654422379082193946, s_id=5753717729389507729, p_id=0] trace=unnamed-java-app/trace.annotation/Main.main metrics={} tags={component=trace, language=jvm, runtime-id=fedd6bdd-2499-451c-bb36-0f662f3e8648, thread.id=1, thread.name=main}, duration_ns=0
2019-12-02 09:23:41.892  INFO 3998 [atcher-worker-2] Main                                     : DefaultDispatcher-worker-2 DDSpan [ t_id=2654422379082193946, s_id=1179641962218689830, p_id=5753717729389507729] trace=unnamed-java-app/trace.annotation/Main.waitFor metrics={} tags={Iteration=0, component=trace, thread.id=26, thread.name=DefaultDispatcher-worker-2}, duration_ns=0
2019-12-02 09:23:41.893  INFO 3998 [atcher-worker-2] Main                                     : DefaultDispatcher-worker-2 DDSpan [ t_id=2654422379082193946, s_id=1179641962218689830, p_id=5753717729389507729] trace=unnamed-java-app/trace.annotation/Main.waitFor metrics={} tags={Iteration=0, component=trace, thread.id=26, thread.name=DefaultDispatcher-worker-2}, duration_ns=0
2019-12-02 09:23:41.899  INFO 3998 [atcher-worker-3] Main                                     : DefaultDispatcher-worker-3 DDSpan [ t_id=2654422379082193946, s_id=2170501652924201656, p_id=5753717729389507729] trace=unnamed-java-app/trace.annotation/Main.waitFor metrics={} tags={Iteration=1, component=trace, thread.id=27, thread.name=DefaultDispatcher-worker-3}, duration_ns=0
2019-12-02 09:23:41.900  INFO 3998 [atcher-worker-4] Main                                     : DefaultDispatcher-worker-4 DDSpan [ t_id=2654422379082193946, s_id=6175891178351208339, p_id=5753717729389507729] trace=unnamed-java-app/trace.annotation/Main.waitFor metrics={} tags={Iteration=3, component=trace, thread.id=28, thread.name=DefaultDispatcher-worker-4}, duration_ns=0
2019-12-02 09:23:41.899  INFO 3998 [atcher-worker-1] Main                                     : DefaultDispatcher-worker-1 DDSpan [ t_id=2654422379082193946, s_id=8485714618033939065, p_id=5753717729389507729] trace=unnamed-java-app/trace.annotation/Main.waitFor metrics={} tags={Iteration=2, component=trace, thread.id=25, thread.name=DefaultDispatcher-worker-1}, duration_ns=0
2019-12-02 09:23:41.900  INFO 3998 [atcher-worker-3] Main                                     : DefaultDispatcher-worker-3 DDSpan [ t_id=2654422379082193946, s_id=2170501652924201656, p_id=5753717729389507729] trace=unnamed-java-app/trace.annotation/Main.waitFor metrics={} tags={Iteration=1, component=trace, thread.id=27, thread.name=DefaultDispatcher-worker-3}, duration_ns=0
2019-12-02 09:23:41.902  INFO 3998 [atcher-worker-5] Main                                     : DefaultDispatcher-worker-5 DDSpan [ t_id=2654422379082193946, s_id=3463457279142380734, p_id=5753717729389507729] trace=unnamed-java-app/trace.annotation/Main.waitFor metrics={} tags={Iteration=4, component=trace, thread.id=29, thread.name=DefaultDispatcher-worker-5}, duration_ns=0
2019-12-02 09:23:41.903  INFO 3998 [atcher-worker-6] Main                                     : DefaultDispatcher-worker-6 DDSpan [ t_id=2654422379082193946, s_id=4348256562928250323, p_id=5753717729389507729] trace=unnamed-java-app/trace.annotation/Main.waitFor metrics={} tags={Iteration=6, component=trace, thread.id=30, thread.name=DefaultDispatcher-worker-6}, duration_ns=0
2019-12-02 09:23:41.904  INFO 3998 [atcher-worker-7] Main                                     : DefaultDispatcher-worker-7 DDSpan [ t_id=2654422379082193946, s_id=4421324058712740639, p_id=5753717729389507729] trace=unnamed-java-app/trace.annotation/Main.waitFor metrics={} tags={Iteration=5, component=trace, thread.id=31, thread.name=DefaultDispatcher-worker-7}, duration_ns=0
2019-12-02 09:23:41.905  INFO 3998 [atcher-worker-8] Main                                     : DefaultDispatcher-worker-8 DDSpan [ t_id=2654422379082193946, s_id=8537738308186511090, p_id=5753717729389507729] trace=unnamed-java-app/trace.annotation/Main.waitFor metrics={} tags={Iteration=7, component=trace, thread.id=32, thread.name=DefaultDispatcher-worker-8}, duration_ns=0
2019-12-02 09:23:41.906  INFO 3998 [tcher-worker-10] Main                                     : DefaultDispatcher-worker-10 DDSpan [ t_id=2654422379082193946, s_id=1985917427252727825, p_id=5753717729389507729] trace=unnamed-java-app/trace.annotation/Main.waitFor metrics={} tags={Iteration=9, component=trace, thread.id=34, thread.name=DefaultDispatcher-worker-10}, duration_ns=0
2019-12-02 09:23:41.909  INFO 3998 [tcher-worker-13] Main                                     : DefaultDispatcher-worker-13 DDSpan [ t_id=2654422379082193946, s_id=7556649444744964666, p_id=5753717729389507729] trace=unnamed-java-app/trace.annotation/Main.waitFor metrics={} tags={Iteration=8, component=trace, thread.id=37, thread.name=DefaultDispatcher-worker-13}, duration_ns=0
2019-12-02 09:23:42.942  INFO 3998 [atcher-worker-3] Main                                     : DefaultDispatcher-worker-3 DDSpan [ t_id=8401234232036504370, s_id=481145876484775946, p_id=0] trace=unnamed-java-app/trace.annotation/Main.waitFor metrics={} tags={component=trace, language=jvm, runtime-id=fedd6bdd-2499-451c-bb36-0f662f3e8648, thread.id=27, thread.name=DefaultDispatcher-worker-3}, duration_ns=0
[dd.trace 2019-12-02 09:23:43:587 +0100] [dd-trace-writer] WARN datadog.trace.agent.common.writer.DDApi - Error while sending 3 of 6 traces to the DD agent. java.net.ConnectException: Failed to connect to localhost/0:0:0:0:0:0:0:1:8126 (going silent for 5 minutes)
2019-12-02 09:23:43.946  INFO 3998 [atcher-worker-1] Main                                     : DefaultDispatcher-worker-1 DDSpan [ t_id=1567002908161829012, s_id=857742505563499203, p_id=0] trace=unnamed-java-app/trace.annotation/Main.waitFor metrics={} tags={component=trace, language=jvm, runtime-id=fedd6bdd-2499-451c-bb36-0f662f3e8648, thread.id=25, thread.name=DefaultDispatcher-worker-1}, duration_ns=0
2019-12-02 09:23:44.951  INFO 3998 [atcher-worker-7] Main                                     : DefaultDispatcher-worker-7 DDSpan [ t_id=2602817791208366767, s_id=926635854296932000, p_id=0] trace=unnamed-java-app/trace.annotation/Main.waitFor metrics={} tags={component=trace, language=jvm, runtime-id=fedd6bdd-2499-451c-bb36-0f662f3e8648, thread.id=31, thread.name=DefaultDispatcher-worker-7}, duration_ns=0
2019-12-02 09:23:45.953  INFO 3998 [atcher-worker-7] Main                                     : DefaultDispatcher-worker-7 DDSpan [ t_id=7206127938673410068, s_id=6063878620474869192, p_id=0] trace=unnamed-java-app/trace.annotation/Main.waitFor metrics={} tags={component=trace, language=jvm, runtime-id=fedd6bdd-2499-451c-bb36-0f662f3e8648, thread.id=31, thread.name=DefaultDispatcher-worker-7}, duration_ns=0
2019-12-02 09:23:46.957  INFO 3998 [atcher-worker-7] Main                                     : DefaultDispatcher-worker-7 DDSpan [ t_id=4634851895448235703, s_id=3472674439697055107, p_id=0] trace=unnamed-java-app/trace.annotation/Main.waitFor metrics={} tags={component=trace, language=jvm, runtime-id=fedd6bdd-2499-451c-bb36-0f662f3e8648, thread.id=31, thread.name=DefaultDispatcher-worker-7}, duration_ns=0
2019-12-02 09:23:47.963  INFO 3998 [atcher-worker-7] Main                                     : DefaultDispatcher-worker-7 DDSpan [ t_id=3557106430153605668, s_id=8402253980305412920, p_id=0] trace=unnamed-java-app/trace.annotation/Main.waitFor metrics={} tags={component=trace, language=jvm, runtime-id=fedd6bdd-2499-451c-bb36-0f662f3e8648, thread.id=31, thread.name=DefaultDispatcher-worker-7}, duration_ns=0
2019-12-02 09:23:48.967  INFO 3998 [atcher-worker-7] Main                                     : DefaultDispatcher-worker-7 DDSpan [ t_id=6980458062161759504, s_id=665376991271971413, p_id=0] trace=unnamed-java-app/trace.annotation/Main.waitFor metrics={} tags={component=trace, language=jvm, runtime-id=fedd6bdd-2499-451c-bb36-0f662f3e8648, thread.id=31, thread.name=DefaultDispatcher-worker-7}, duration_ns=0
2019-12-02 09:23:49.972  INFO 3998 [atcher-worker-7] Main                                     : DefaultDispatcher-worker-7 DDSpan [ t_id=4854400913329068137, s_id=7033435703755271922, p_id=0] trace=unnamed-java-app/trace.annotation/Main.waitFor metrics={} tags={component=trace, language=jvm, runtime-id=fedd6bdd-2499-451c-bb36-0f662f3e8648, thread.id=31, thread.name=DefaultDispatcher-worker-7}, duration_ns=0
```

As you can see only traces with iteration 0 has correct info as it actually don't await for anything. All the other coroutines has their's spans info lost and recreated as if it was parent span.

## How to start?

`./gradlew run`