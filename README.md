Welcome to DataflowEx
===================
DataflowEx is a collection of extensions to TPL Dataflow library with Object-Oriented Programming in mind. It does not replace TPL Dataflow but provides abstraction/management on top of dataflow blocks to make your life easier.

If you are not familiar with TPL Dataflow yet, please take your time to watch two videos:

Beginner (15min):
http://channel9.msdn.com/posts/TPL-Dataflow-Tour

Advanced (63min):
http://channel9.msdn.com/posts/TPL-Dataflow-Tour

And detailed documentation here:
http://msdn.microsoft.com/en-us/library/hh228603(v=vs.110).aspx

So, what's wrong with TPL Dataflow? Nothing. The library from Microsoft library looks simply great. However, in the tough real world there are some obstacles when we apply **RAW** TPL Dataflow. Let's look at an example:

```c#
var splitter = new TransformBlock<string, KeyValuePair<string, int>>(
    input =>
        {
            var splitted = input.Split('=');
            return new KeyValuePair<string, int>(splitted[0], int.Parse(splitted[1]));
        });

var dict = new Dictionary<string, int>();
var aggregater = new ActionBlock<KeyValuePair<string, int>>(
    pair =>
        {
            int oldValue;
            dict[pair.Key] = dict.TryGetValue(pair.Key, out oldValue) ? oldValue + pair.Value : pair.Value;
        });

splitter.LinkTo(aggregater, new DataflowLinkOptions() { PropagateCompletion = true});

splitter.Post("a=1");
splitter.Post("b=2");
splitter.Post("a=5");

splitter.Complete();
aggregater.Completion.Wait();
Console.WriteLine("sum(a) = {0}", dict["a"]); //prints sum(a) = 6

```
