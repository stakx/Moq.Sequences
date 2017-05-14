# Moq.Sequences #

Allows verifying whether method calls in a Moq setup are invoked in the correct order.

[![Build status](https://ci.appveyor.com/api/projects/status/5ah4sutdta29j3k4?svg=true)](https://ci.appveyor.com/project/stakx/moq-sequences)


## Features ##

* Supports method invocations, property setters and getters.
* Allows you to specify the number of times a specific call should be expected.
* Provides loops which allow you to group calls into a recurring group.
* Allows you to specify the the number of times a loop should be expected.
* Calls that are expected to be called in sequence can be inter-mixed with calls that are expected in any order.
* Multi-threaded support.

## To Use ##

Add a reference to the NuGet package [stakx.Moq.Sequences](https://www.nuget.org/packages/stakx.Moq.Sequences) to your .NET project.

### Sequences ###

You create a Sequence as an envelope for both the expectations and the corresponding mock execution.

Sequences are created by calling `Sequence.Create()` and putting this in a `using` block.

During mock execution, any violation of sequencing will throw a `SequenceException` immediately.
When the Sequence is disposed any sequencing expectations that were not fulfilled will cause a 
`SequenceException` to be thrown.

There can only be one `Sequence` active per thread. An attempt to create more than one will throw a `SequenceUsageException`.

### Steps ###

To specify that a call is expected append `.InSequence()` to the `mock.Setup()` method.
You can optionally include the number of times the call is expected - the default is once.
To enforce a simple sequence do the following:

```csharp
    // using Moq.Sequences;

    using (Sequence.Create())
    {
        mock.Setup(_ => _.Method1()).InSequence();
        mock.Setup(_ => _.Method2()).InSequence(Times.AtMostOnce());
        …
		// Logic that triggers the above method calls should be done here.
        …
    }
```

Note that the order expected is the order in which the `Setup` methods execute.

### Loops ###

You can specify that a group of calls should be done in sequence multiple times.
An example could be a test that expects a resource to be opened, read and then closed where these operations should always be done
in sequence the same number of times.

You create a Loop by calling `Sequence.Loop()` where any number of iterations is allowed, or `Sequence.Loop(Times)` if you want
to restrict the number of times the loop executes.

```csharp
    // using Moq.Sequences;

    using (Sequence.Create())
    {
        …
        using (Sequence.Loop(Times.Exactly(3)))
        {
            mock.Setup(_ => _.Method1()).InSequence();
            mock.Setup(_ => _.Method2()).InSequence();
        }
        …
		// Logic that triggers the above method calls should be done here.
        …
    }
```

## To Build ##

Both the source code and a NuGet package can be built with Visual Studio 2017 (Community Edition or better).
