---
title: "String Classifier: A story"
date: 2020-10-21T09:52:26-04:00
draft: true
---
# A brief history of what I'm even going on about.
I've got this idea for how to automatically classify and produce a matching algorithm for an unbounded stream of small strings with slight differences, and I think it might be fun to detail how it all works.

## How it started
So, a while ago I was working on a project that involved needing to collect and classify automated emails.
The we decided to classify them based on their subject lines, which tended to be similar, but had minor differences:
```
Hello [XYZ]! There's a problem with (FOO)!
Variety is the spice of life, right [QUX]?
Hello [ABC]! There's a problem with (BAR)!
Variety is the spice of life, right [QUIZ]?
Hello [HIJ]! There's a problem with (BAZ)!
Some random, unexpected string.
```

{{<aside>}}
There's some performance penalties to doing things this way, but given the volumes we were dealing with, they didn't 
stand out as particularly troubling.  
Some better methods of approaching this problem will be discussed later.
// This is a coment about the regex being slow, and how needing to run an unbounded number of regex over each message is maybe really slow.
// the solution is the dfa, which can run all the regex in parallel, since it works as a classifier rather than a acceptor.

{{</aside>}}
Since these messages were machine generated from templates, it didn't seem like too much work to make a system
that would run a regex over incoming messages, and then classify them based on which one matched.


{{%aside class="info"%}}
"regex" is short hand for a variant of what are called "regular expressions".  
A regular expression is a compact system for describing the format
of a string of text.
A small regular expression `a*` would match the 'a' character zero or more times.
so it would match
```
a
aa
aaa
```
and so on, as well as any other string, since every string has at least zero 'a's in it.

A more complicated example would be `a(b|c\d?)+`, which roughly transaltes to "An 'a', followed by one or more instances of either one 'b', or one 'c' possibly followed by any number".
As you can tell, this can become quite complex, and describe many different strings in a very compact notation.
{{%/aside%}}

Now, since we didn't control the source of these messages, we couldn't control when the set of messages that we needed to work with changed.
This meant that at some interval, unknown to us, new message classifications would show up, and we wouldn't be able to accurately label them.  
This shouldn't be too big of an issue, since we can label those messages as "unknown", and since we're only interested in a general breakdown, not 
a specific, per message tracking, it should be fine to have someone get an alert and go write a regex when a surge of new messages starts coming in.  

Turns out that wasn't the case, and eventually the project faltered and was decomissioned.

## How could that have been prevented?

{{<aside>}}
The issue was actually a poorly defined value proposition for the project, but that isn't interesting and was solved by cancelling it.
{{</aside>}}
The issue came down to the problem of classifying the unknown.


Since we didn't know what to do with the unknown messages, we were left with needing to involve humans in the mix, since humans are good at *classification* and *pattern recognition*.

For a while, while the project was being reevaluated in light of these limitations, I spent some time investigating ways to try to cut humans out of the loop, in some small way.  
I came across a variety of different techniques for ways that you can automatically group a mostly unknown collection of things, and came up with some ideas for how to use that information to produce a regex to match the data.  
Unfortunatly, none of them was able to opperate with acceptable performance (best case scenario was $O(n^2)$, which was unacceptable given the unbounded nature of $n$), so the plans for salvation were set aside, and new projects moved onto.
{{<aside class="info">}}
$O(n^2)$ refers to what is called "Big-O" notation.  It's a measure of average performance for a computer algorithm, in terms of the "size" of the input, leaving off anything that isn't the part that grows the fastest.  
It's a helpful shortcut for talking about efficiency and performance, where smaller is typically better.
{{</aside>}}

But, the notion of how to do this right never stopped nagging at the back of my mind.

## Cut to now

{{<aside>}}
It was actually totally related, just not on the face of it.
{{</aside>}}
A few years later, while looking into an entirely unrelated problem, I came across some techniques for processing text that allowed for it to be operated on *much* more efficient manner, and made the whole thing tractable again.  

So now that it's tractable again, I've decided to revitalize the effort, with a more focusted goal:
**Identify log messages that you should look at.**
Since log messages are also relatively consistently formatted, new ones may be added without notice, and they would benefit from filtering, they seem a natural fit for the problem domain.
Additionally, having a lot of "log noise" is common, so a tool that would only display the messages that conveyed more *information* would be valuable.
High information log messages should probably be brought to the attention of a human.

#### Why log messages?
If you talk to anyone with a lot of log output, they'll tell you that a lot of it can be ignored most of the time.
They'll frequently have alerts setup for the conditions that are really scary.  
But even in the conditions that aren't scary, if you explore them, you can find different scenarios where maybe there should be an alert, but only under some threshold conditions.
So the goal of the tool is to automatically detect those pathological cases where the logs are weird, but you wouldn't have thought
to document that this was weird upfront.

### A quick note on formatting
Throughout this post, I'm going to be using some formatting to convey meaning.

{{<aside>}}
A blase and pointless remark
{{</aside>}}
The first piece of formatting is: The remark.
I'll be using this for commentary or side comments that aren't directly related to what's being said, but that I wanted to say anyway.

The next is
{{<aside class="info">}}
Extra information
{{</aside>}}
This is for pieces of information that go a bit more in-depth explaining the background context of some statemnt that I've made.

As I've already used both of those formatting conventions rather excessively, hopefully their point is relatively clear.  
Anyway, feel free to ignore the side remarks, and skip the extra info if nothing came across as mysterious.

# So what *wasn't* working?
#### Where I came from, and terminology definitions
The general "theme" to what didn't pan out was: It kinda worked, but if you keep putting stuff in, it'll become very slow or
use incredible resources, and actually always do both.

## Honey nut data clustering algorithms
Clustering is the the process of comparing things to other things, and then putting
things that are similar next to each other.  
It's what sane people do with their skittles before eating them.
### Actually grouping things
Putting aside the comparison question for the moment, we're left with the question of grouping.

#### affinity propagation
#### DBSCAN

### How do we compare strings?
The relationship between distance and similarity.
Things should be grouped together if they're close on a map,
and the skittles should be grouped together if their color is very similar.

High similarity is the same as low distance.
If we normalize it to a scale of 0 to 1: $ D = 1 - S $

#### Levenshtein?
#### Longest Common Subsequence?
#### Something else?

## Regex generation
### I dunno... More LCS?

# So what *might* be working?

# Enter: The min-hash

# What if trees?

# Density: Revisited
#### DenStream

# Hopcroft?
# Brzozowski?

# RPNI
## Wait, someone already did all this?
### Am I that bad a google?

# What is information?
# How do we know which messages have more information?
## good-turing?
## bandwidth?
## probability?
<!-- 
```go
go func() {
	for event := range eventStream {
		switch event.Type {
	default:
			fmt.Println(event.Text)
		}
	}
}()
```

$$\int_{a}^{b} x^2 dx$$

Integrate $\int x^3 dx$ to get the answer.
-->
