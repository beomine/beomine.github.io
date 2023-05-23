---
layout: post
title: C# Never Ending Task
author: sarah
date: 2023-05-23 01:36:00 +0900
categories: [Study, C#]
tags: [C#, CS, Task, 무한루프]
---

## 무한루프(While문) 쓰기 싫을 때

#### NeverEndingTask.cs
``` cs
public abstract class NeverEndingTask
{
    private CancellationTokenSource? wtoken;
    private ITargetBlock<DateTimeOffset>? _task;

    public abstract Task DoAsync(CancellationToken cancellationToken);

    ITargetBlock<DateTimeOffset> CreateNeverEndingTask(
       Func<DateTimeOffset, CancellationToken, Task> action,
       CancellationToken cancellationToken)
    {
        if (action == null) throw new ArgumentNullException(nameof(action));

        ActionBlock<DateTimeOffset>? block = null;
        block = new ActionBlock<DateTimeOffset>(async now =>
        {
            await action(now, cancellationToken).ConfigureAwait(false);
            //await Task.Delay(TimeSpan.FromSeconds(10), cancellationToken).ConfigureAwait(false);

            block?.Post(DateTimeOffset.Now);
        }, new ExecutionDataflowBlockOptions
        {
            CancellationToken = cancellationToken
        });

        return block;
    }

    public void StartNeverEndingTask()
    {
        try
        {
            wtoken = new CancellationTokenSource();

            _task = CreateNeverEndingTask((now, ct) => DoAsync(ct), wtoken.Token);
            _task.Post(DateTimeOffset.Now);
        }
        catch (Exception ex)
        {
            Debug.WriteLine($"{ex}");
        }

    }

    public void StopNeverEndingTask()
    {
        using (wtoken)
        {
            wtoken?.Cancel();
        }
        wtoken?.Dispose();
        wtoken = null;
        _task = null;
    }
}
```

#### ConcreteNeverEndingTask.cs
``` cs
public class ConcreteNeverEndingTask : NeverEndingTask
{
    public override async Task DoAsync(CancellationToken cancellationToken)
    {
        Console.WriteLine("DO ASYNC TASK");

        await Task.Delay(TimeSpan.FromMilliseconds(1000), cancellationToken).ConfigureAwait(false);
    }
}
```

#### main.cs
``` cs
ConcreteNeverEndingTask neverEndingTask = new();
neverEndingTask.StartNeverEndingTask();
neverEndingTask.StopNeverEndingTask();
```