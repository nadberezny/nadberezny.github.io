---
layout: post
title:  "Advanced Session Windows in Flink"
date:   2024-08-04 22:05:44 +0200
categories: jekyll update
---
[Session windows][session-docs] are a type of window that groups elements by sessions of activity. 
A session is defined as a period of activity separated by a specified gap of inactivity. When the gap of inactivity is detected, the session window is triggered and the elements in the session are processed. 
This blog post will not cover the basics of session windows, but rather focus on advanced topics related to session windows in Flink.
Instead, we will focus on advanced topics related to session windows in Flink. In certain scenarios, you might find the provided session window interface as limiting.
One of those cases is when you need to have an alternative way of defining triggers that will close and release sessionized elements.
The cool thing about Flink is that you are limitless with ProcessFunction. You are free to implement your custom logic for session windows.
But it's not our intent here. We will do our best to use high level building blocks provided by Flink in order to accomplish our goal.

So how do we start? The best way is always to be lead by the code, and the interfaces that are already provided. 
Let's take a close look at `EventTimeSessionWindows.withGap(Duration gap)` method.
What we get in return is an implementation of MergingWindowAssigner.
```java
public abstract class MergingWindowAssigner extends WindowAssigner<..., ...> {
    public abstract void mergeWindows(Collection<W> windows, ...);
// ...
}
```
We already know that session window, for every incoming event creates a new window holding this newly arrived event. Afterwards it tries to merge overlapping windows, by calling `mergeWindows` method.
```java
public class SessionTimeWindow extends Window {
    // ... 
}
```

[session-docs]: https://nightlies.apache.org/flink/flink-docs-release-1.19/docs/dev/datastream/operators/windows/#session-windows
[merge-windows-method]: https://github.com/apache/flink/blob/master/flink-streaming-java/src/main/java/org/apache/flink/streaming/api/windowing/windows/TimeWindow.java#L208
