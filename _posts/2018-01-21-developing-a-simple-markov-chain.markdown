---
layout: post
title:  "Syntactically similar texts with Markov Chains"
date:   2014-08-19 23:56:45
description: A ruby implementation and explanation of a Markov Chain 
categories:
permalink: syntactic-markov-chain
---

Driving home one evening, a CBC segment detailed a software system being developed to generate story writing axioms from an input corpus of text. I found this interesting. It's apparent modern technology provides us with tools to analyze and manipulate texts in an unprecedented way. Naturally, this process works to generate similar text as well, and can be seen affecting the world directly today: twitter propaganda bots, computer generated advertisements, natural language assistants, and so forth.

One basic mechanism for implementing these systems is the Markov Chain. In essence, it is a state machine wherein nodes are connected by probabilities. Generally, when applied to textual data, it generates an output that is syntactically similar but semantically gibberish. It looks similar to the input corpus - same words, common phrasings, and so forth, but has no logical thread connecting ideas, as the algorithm isn't nearly that smart.

This is because the algorithm analyzes the occurrence of words following a word. More technically, for every *prefix*, there exists a set of *suffixes*. Each of these suffixes can be given a probability based on the frequency of it's occurrence in the set of suffixes. Knowing this, we can create a table to represent each prefix in the text and it's set of suffixes.

In the following example, prefixes are understood as two words, and a *NONWORD* denotes a space, ie 'The quick' or 'NONWORD The'

{% highlight ruby %}
#Create a table representing a prefix (key) and the common suffixes (value) for that prefix
#In this representation, prefixes are 2 words, or N = 2
def freq_table(data)

  freq_table = {}
  x = NONWORD
  y = NONWORD

  data.each do |word|
    (freq_table["#{x} #{y}"] ||= []) << word
    x = y
    y = word
  end

  (freq_table["#{x} #{y}"] ||= []) << NONWORD

  freq_table

end

{% endhighlight %}

With this data structure, generating output is simple. Using *NONWORD* *NONWORD*, or the very start of the input, iterate and randomly sample from each prefix a suffix, creating a new prefix from which to query a new suffix.

{% highlight ruby %}
#Generate the output by sampling from our frequency table
def output(table)
  
  output = ""
  x = NONWORD
  y = NONWORD

  OUTPUT_WORDS.times do
    word = table["#{x} #{y}"].sample 
    break if word == NONWORD
    output << "#{word} "
    x = y
    y = word
  end

  output

end
{% endhighlight %}

So, an input like:

> Stay home Be bored (it's crap, I KNOW) Ooh, Glenn Don't come to the house tonight Ooh, Glenn Don't come to the house tonight Because there's somebody here Who'll take a hatchet to your ear How the frustration renders me Hateful, Glenn! Don't come to the house tonight Don't come to the house tonight Because you'll slip on the Trail of my bespattered remains And so, that's why GOODBYE MY LOVE, GOODBYE MY LOVE GOODBYE MY LOVE, GOODBYE MY LOVE GOODBYE MY LOVE, GOODBYE MY LOVE I won't share you I won't share you With the drive The ambition And the zeal I feel This is my time As the note I wrote Was read, she said Has the Perrier gone Straight to my head Or is life plainly sick and cruel, instead? "YES!" No - no - no - no - no - no I won't share you I won't share you With the drive And the dreams inside This is my time Life tends to come and go Well, that's OK Just as long as you know Life tends to come and go Well, that's OK Just as long as you know I won't share you I won't share you With the drive And the dreams inside This is my time This is my time

Might produce:

> "With the drive And the dreams inside This is my time This is my time This is my time This is my time This is my time As the note I wrote Was read, she said Has the Perrier gone Straight to my head Or is life plainly sick and cruel, instead? YES! No - no - no - no I won't share you With the drive And the dreams inside This is my time As the note I wrote Was read, she said Has the Perrier gone Straight to my head Or is life plainly sick and cruel, instead? YES! No - no - no - no - no - no - no - no - no - no - no - no - no - no - no - no - no - no - no I won't share you I won't share you I won't share you I won't share you I won't share you "

The larger the input, the better the results.

If you'd like to try running the script in full, I've provided some input data and instructions in a [github](https://github.com/laaksomavrick/markov-chain-text-generator) repository.
