---
layout: post
title: Object-oriented programming for scientists
comments: true
tags:
  - python
  - bioinformatics
  - object-oriented programming
---

<figure>
  <img src="/images/sandcastle.jpg" alt="Sandcastle" />
  <figcaption>
  Using a bucket to create sandcastles. That is what object-oriented
  programming is all about.
  </figcaption>
</figure>

## Introduction

For anyone not familiar with object-oriented programming it can sometimes come
across as something mysterious that is used by expert coders. Indeed, any
respectable text book on object-oriented programming will try to overwhelm the
reader with concepts such as "abstraction", "encapsulation", "inheritance" and
"polymorphism".

However, object-oriented programming is not that difficult and can be very
useful when dealing with complex data structures. In this post I will
illustrate some object-oriented principles using a bioinformatics example, the
parsing of [FASTA files](http://en.wikipedia.org/wiki/FASTA_format).

The code will be written in Python as I like it, it has built-in support for
object-oriented programming and its syntax is relatively easy to understand.

## An example using procedural programming

To set the scene let us write some code using procedural programming to parse
the ``example.fasta`` file below.

```
>sp|O76074|PDE5A_HUMAN cGMP-specific 3',5'-cyclic phosphodiesterase OS=Homo sapiens GN=PDE5A PE=1 SV=2
MERAGPSFGQQRQQQQPQQQKQQQRDQDSVEAWLDDHWDFTFSYFVRKATREMVNAWFAE
RVHTIPVCKEGIRGHTESCSCPLQQSPRADNSAPGTPTRKISASEFDRPLRPIVVKDSEG
TVSFLSDSEKKEQMPLTPPRFDHDEGDQCSRLLELVKDISSHLDVTALCHKIFLHIHGLI
SADRYSLFLVCEDSSNDKFLISRLFDVAEGSTLEEVSNNCIRLEWNKGIVGHVAALGEPL
NIKDAYEDPRFNAEVDQITGYKTQSILCMPIKNHREEVVGVAQAINKKSGNGGTFTEKDE
KDFAAYLAFCGIVLHNAQLYETSLLENKRNQVLLDLASLIFEEQQSLEVILKKIAATIIS
FMQVQKCTIFIVDEDCSDSFSSVFHMECEELEKSSDTLTREHDANKINYMYAQYVKNTME
PLNIPDVSKDKRFPWTTENTGNVNQQCIRSLLCTPIKNGKKNKVIGVCQLVNKMEENTGK
VKPFNRNDEQFLEAFVIFCGLGIQNTQMYEAVERAMAKQMVTLEVLSYHASAAEEETREL
QSLAAAVVPSAQTLKITDFSFSDFELSDLETALCTIRMFTDLNLVQNFQMKHEVLCRWIL
SVKKNYRKNVAYHNWRHAFNTAQCMFAALKAGKIQNKLTDLEILALLIAALSHDLDHRGV
NNSYIQRSEHPLAQLYCHSIMEHHHFDQCLMILNSPGNQILSGLSIEEYKTTLKIIKQAI
LATDLALYIKRRGEFFELIRKNQFNLEDPHQKELFLAMLMTACDLSAITKPWPIQQRIAE
LVATEFFDQGDRERKELNIEPTDLMNREKKNKIPSMQVGFIDAICLQLYEALTHVSEDCF
PLLDGCRKNRQKWQALAEQQEKMLINGESGQAKRN
>sp|Q9Y233|PDE10_HUMAN cAMP and cAMP-inhibited cGMP 3',5'-cyclic phosphodiesterase 10A OS=Homo sapiens GN=PDE10A PE=1 SV=1
MRIEERKSQHLTGLTDEKVKAYLSLHPQVLDEFVSESVSAETVEKWLKRKNNKSEDESAP
KEVSRYQDTNMQGVVYELNSYIEQRLDTGGDNQLLLYELSSIIKIATKADGFALYFLGEC
NNSLCIFTPPGIKEGKPRLIPAGPITQGTTVSAYVAKSRKTLLVEDILGDERFPRGTGLE
SGTRIQSVLCLPIVTAIGDLIGILELYRHWGKEAFCLSHQEVATANLAWASVAIHQVQVC
RGLAKQTELNDFLLDVSKTYFDNIVAIDSLLEHIMIYAKNLVNADRCALFQVDHKNKELY
SDLFDIGEEKEGKPVFKKTKEIRFSIEKGIAGQVARTGEVLNIPDAYADPRFNREVDLYT
GYTTRNILCMPIVSRGSVIGVVQMVNKISGSAFSKTDENNFKMFAVFCALALHCANMYHR
IRHSECIYRVTMEKLSYHSICTSEEWQGLMQFTLPVRLCKEIELFHFDIGPFENMWPGIF
VYMVHRSCGTSCFELEKLCRFIMSVKKNYRRVPYHNWKHAVTVAHCMYAILQNNHTLFTD
LERKGLLIACLCHDLDHRGFSNSYLQKFDHPLAALYSTSTMEQHHFSQTVSILQLEGHNI
FSTLSSSEYEQVLEIIRKAIIATDLALYFGNRKQLEEMYQTGSLNLNNQSHRDRVIGLMM
TACDLCSVTKLWPVTKLTANDIYAEFWAEGDEMKKLGIQPIPMMDRDKKDEVPQGQLGFY
NAVAIPCYTTLTQILPPTEPLLKACRDNLSQWEKVIRGEETATWISSPSVAQKAAASED
```

The aim is to find and print out the FASTA record with the UniProt identifier
``Q9Y233`` (the second entry). The code below achieves this using procedural
programming.

```python
with open('example.fasta') as fh:
    match = False
    for line in fh:
        line = line.strip()  # Remove newline at the end of the line.
        if line.startswith('>'):
            # We have encountered a description line. That means the start of a
            # new FASTA record.
            if line.find('Q9Y233') != -1:
                # We have matched our search criteria.
                match = True
            else:
                # We have encountered a new entry and it does not match the
                # search criteria.
                match = False
        if match:
            # We are currently in a section of the FASTA file that matches our
            # search criteria.
            print(line)
```

It is worth noting that I felt the need to add quite a few comments to explain
what was going on, a sign that everything is not as clear as it should be.
However, on the whole the code does a job and it works.

Now imagine that you wanted to add a filter based on the length of the
sequence. Is it immediately obvious what you would do? How can you ensure that
the code remains understandable?


## Object-oriented programming to the rescue

Object-oriented programming is all about grouping data and functionality
together. This allows one to abstract away some of the complexities of the
processing logic and to encapsulate the data.

Let us start by creating an object representing a FASTA record. Save the code
below to a file named ``fasta.py``.

```python
class FastaRecord(object):
    """Class representing a FASTA record."""

    def __init__(self, description_line):
        """Initialise an instance of the FastaRecord class."""
        self.description = description_line.strip()
        self.sequences = []

    def add_sequence_line(self, sequence_line):
        """
        Add a sequence line to the FastaRecord instance.
        This function can be called more than once.
        """
        self.sequences.append( sequence_line.strip() )

    def __repr__(self):
        """Representation of the FastaRecord instance."""
        lines = [self.description,]
        lines.extend(self.sequences)
        return '\n'.join(lines)
```

There are a few things to note in the code above. Particularly if you are new
to object-oriented programming and/or Python.

First of all we inherit functionality from the base class ``object`` (the first
line). This is kind of historical where in Python 2.1 "new style" classes
were added. To remain backwards compatible with the "classic" or "old style"
classes it was decided that one would have to inherit from ``object`` to access
the goodness of the new style class. There are more details on the [Python
wiki](https://wiki.python.org/moin/NewClassVsClassicClass).

Secondly, we make use of the "magic" method ``__init__``. This is used to create
an instance of a class.

*Classes, objects, instances, what is up with all this terminology? What does
it all mean?*

Okay, let us take a slight detour. You can think of classes as moulds, for
example a plastic bucket that you bring to the beach to make a sand castle. You
fill the bucket with sand and tip it up-side down, pat it on the top and lift
it up.  What remains is a tower made out of sand. This sand castle is an
"instance" of your bucket "class". Finally, the term "object", as in
object-oriented programming, tends to be used to refer to classes and instances
interchangeably.

Back to the ``__init__`` method, which is used to initialise an instance of
the class. The instance created is accessible via the ``self`` argument. During
the initialisation of the ``FastaRecord`` class we also provide the description
line.

```python
>>> from fasta import FastaRecord
>>> fasta_record = FastaRecord('>sp|O76074|PDE5A_HUMAN')
```

Note that the ``fasta_record`` variable above is an instance of the
``FastaRecord`` class. We can access the ``description`` attribute of the
``FastaRecord`` instance directly.

```python
>>> fasta_record.description
'>sp|O76074|PDE5A_HUMAN'
```

The ``add_sequence_line`` method simply adds a sequence line to the
``sequences`` (list) attribute.

```python
>>> fasta_record.add_sequence_line('MERAGPSFGQQRQQQQPQQQKQQQRDQDSVEAWLDDHWDFTFSYFVRKATREMVNAWFAE')
>>> fasta_record.add_sequence_line('RVHTIPVCKEGIRGHTESCSCPLQQSPRADNSAPGTPTRKISASEFDRPLRPIVVKDSEG')
```

Finally, we have the "magic" ``__repr__`` method. At this point you are
probably screaming out loud, what is a "magic" method? A "magic" method is
basically a way to make an object behave like a built-in Python object. For
example the ``__repr__`` method is used to describe how the instance should be
represented. Let us illustrate this below.

```python
>>> fasta_record
>sp|O76074|PDE5A_HUMAN
MERAGPSFGQQRQQQQPQQQKQQQRDQDSVEAWLDDHWDFTFSYFVRKATREMVNAWFAE
RVHTIPVCKEGIRGHTESCSCPLQQSPRADNSAPGTPTRKISASEFDRPLRPIVVKDSEG
```

For more information on "magic" methods have a look at Rafe Kettler's blog post
[A Guide to Python's Magic
Methods](http://www.rafekettler.com/magicmethods.html).


## A FASTA parser object

Now that we have a basic class for working with FASTA records let us create
another class for parsing FASTA files.

```python
class FastaParser(object):
    """Class for parsing FASTA files."""

    def __init__(self, fpath):
        """Initialise an instance of the FastaParser."""
        self.fpath = fpath

    def __iter__(self):
        """Yield FastaRecord instances."""
        fasta_record = None
        with open(self.fpath, 'r') as fh:
            for line in fh:
                if line.startswith('>'):
                    if fasta_record:
                        yield fasta_record
                    fasta_record = FastaRecord(line)
                else:
                    fasta_record.add_sequence_line(line)
        yield fasta_record
```      

In the example above I have used the ``__iter__`` magic method. This basically
defines the behaviour the class should display when called as an iterator. In
this particular case we want it to ``yield`` ``FastaRecord`` instances as the FASTA
file is parsed.

```python
>>> from fasta import FastaParser
>>> fasta_parser = FastaParser('example.fasta')
>>> for fasta_record in fasta_parser:
...     print(fasta_record.description)
...
>sp|O76074|PDE5A_HUMAN cGMP-specific 3',5'-cyclic phosphodiesterase OS=Homo sapiens GN=PDE5A PE=1 SV=2
>sp|Q9Y233|PDE10_HUMAN cAMP and cAMP-inhibited cGMP 3',5'-cyclic phosphodiesterase 10A OS=Homo sapiens GN=PDE10A PE=1 SV=1
```

## Back to grouping data and functionality

At this point we could write a simple script to loop over the FASTA records and
find the hits of interest. However, where should we add the logic for finding
hits of interest?

I would argue that this is a great opportunity for abstracting away the logic
of identifying a hit by putting it in the ``FastaRecord`` class itself. Let us
extend the class to do this.

```python
class FastaRecord(object):
    """Class representing a FASTA record."""

    def __init__(self, description_line):
        """Initialise an instance of the FastaRecord class."""
        self.description = description_line.strip()
        self.sequences = []

    def add_sequence_line(self, sequence_line):
        """
        Add a sequence line to the FastaRecord instance.
        This function can be called more than once.
        """
        self.sequences.append( sequence_line.strip() )

    def matches(self, search_term):
        """Return True if the search_term is in the description."""
        return self.description.find(search_term) != -1

    def __repr__(self):
        """Representation of the FastaRecord instance."""
        lines = [self.description,]
        lines.extend(self.sequences)
        return '\n'.join(lines)
```

Note the addition of the ``matches`` method above. Also, note that the
addition of more functionality did not make the code any more difficult to
understand.

It is now trivial to write a script to do the analysis that we want.

```python
from fasta import FastaParser

for fasta_record in FastaParser('example.fasta'):
    if fasta_record.matches('Q9Y233'):
        print(fasta_record)
```

Compare the descriptiveness of this code to that of the procedural example at
the beginning of this post.

*But you had to write so much more code to get to this point, is it really
worth it?*

I go back to the scenario outlined earlier in this post. Imagine that you had
to extend the logic of the code to be able to filter based on the length of the
sequence. Which code base would you rather use as a starting point? If you are
unsure, try adding this functionality to both code bases to find out which one
is more extensible.


## Try to avoid re-inventing the wheel

The point of this post was to illustrate object-oriented programming, not to
re-invent the wheel. I used the example of parsing FASTA files in this post as
they are widely used in biological research and are conceptually easy to
understand. However, if you are serious about using Python for bioinformatics I
suggest that you check out [Biopython](http://biopython.org/wiki/Main_Page).


## Conclusion

Object-oriented programming can be very useful when dealing with complex data
structures. In particular it can be used to hide complexity by grouping data
and functionality together.

Furthermore, it can make your code more understandable and extensible.

Finally, do not let your lack of knowledge about "polymorphism" and
"inheritance" hold you back from making use of objects. Yes, these are
interesting topics, and please do read up on them. However, they are not
essential to your use of object-oriented programming (at least not in Python).

I hope you find this post useful and that it has encouraged you to try out
object-oriented programming. Send me a message if you need any help.
