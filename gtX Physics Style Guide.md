# gtX Physics Style Guide

## Introduction

This document defines the writing style and notational conventions of physics exercises hosted by Georgia Tech on the edX platform.

The way the edX platform renders problems has all sorts of quirks and limitations, and is subject to frequent revision. Consider this guide a living document.

## edX Basics

Here we'll go over some of the basics of edX problems.

### Don't Use the edX Editor

It's very clunky and fragile and sometimes loses your work.

### Use a Local Text Editor Instead

The very best way to code problems on edX is to code problems **off** edX and then upload them when you're done. There's a Python script for assembling and importing local text files into an edX-ready archive (along with a specification of the edXML format) [available at this link](https://github.gatech.edu/sdouglas6/gtx-library-export).

The major problem with local editing is that you have to import your problem files into edX before you can test them, but the drawbacks of the edX in-browser editor far outweigh the moderate inconvenience of this approach.

## Writing Style

There's honestly not much narrative flexibility afforded to a problem-writer, so this section will mostly serve as a short list of pitfalls to avoid.

### Careful with Question Marks

This is a bizarrely common issue with problem-writing; only some sentences are questions, all questions should end with a question mark, and nothing that is not a question should end with a question mark.

"What is the magnitude of the block's acceleration." needs a question mark.

"Find the horizontal component of the final velocity?" should not have a question mark.

### Avoid Abusive Nominalization

Nominalization is the process of taking a verb and turning it into a noun. Nominalize "The electron was **forced** upward" and you get "The electron encountered an upward **force**". Nominalization is an extremely important part of scientific language, because it lets us mentally turn *actions* that happen to objects ("being forced") into *objects* themselves (a "force") that can be studied in their own right.

The drawback of nominalization is added verbal complexity. Note how in the first example above, the verb is "forced", but in the nominalized form, the verb is "encountered", which carries a less specific meaning. Since a nominalized verb can no longer act grammatically like a verb, it has to be assisted with a neutral *carrying verb* like "encounter", "carry out", "happen", "occur", and so on. This extra complexity isn't a problem as long as you're nominalizing for a good reason.

When a writer nominalizes a verb for no good reason, though, that's called *abusive nominalization*. It adds complexity without actually adding any meaning, and it's a problem endemic in science writing.

All this is to say that if you find your eyes glazing over while reading a sentence, see if there's too much nominalization going on. If you read "a collision occurred between two marbles", just rewrite it to say "two marbles collided."

## Notation Style

edX uses MathJax for its rendering engine, which afforts a broad subset of TeX functionality for equations.

### Scientific Notation

Use scientific notation, not engineering notation. Don't exceed 4 significant digits unless you have a good reason. This is consistent with the scientific notation style used in our other course materials *but is kind of a pain in the butt in edX*.

All the numerical formatting has to be done in Python, including the truncation of significant digits. If you're generating random numerical values for the problem, the easiest thing to do is to generate an integer, store it, and use the integer directly when displaying the value in the body of the problem along with a hard-coded TeX exponent. When you're doing calculations with the value, just multiply the stored integer by whatever power of ten you need.

However, sometimes you won't be able to hard-code a TeX exponent (e.g., if you're using random values to calculate a product which could range over several orders of magnitude). In this case, insert the following code into the top of the problem file:

	# separate significand and exponent
	def frexp_10(decimal):
		parts = ("%e" % decimal).split('e')
		return float(parts[0]), int(parts[1])

This will take a value and return the significand and exponent, respectively.

The following code serves as an example; `v_i` here is a random integer which, when squared, could produce a `K_i` of several different orders of magnitude.

	# initial kinetic energy in J
	K_i = 0.5*m_e*(v_i * 10**6)**2 # J
	K_i_sig, K_i_exp_disp = frexp_10(K_i)   # the exponent is always an integer
	K_i_sig_disp = '{:.3f}'.format(K_i_sig) # four sig figs only
	
Then, in the body of the problem, you can generate scientific notation like so:

	The electron has an initial kinetic energy of
	\($K_i_sig_disp \times 10^{$K_i_exp_disp} \text{J}\))
	
### Units

Every dimensional numerical value should have a unit, both in the problem description and as a label by the response boxes. Use the TeX `\text{}` command to format the unit—this visually distinguishes units from variables. To stick a unit at the end of a text box, use the `trailing_text` property like so:

	<numericalresponse answer="0">
		<description>\(B_{ex} = \)</description>
		<formulaequationinput label="B_mov_x" trailing_text="\(\text{T}\)"/>
		<responseparam type="tolerance" default="3%"/>
	</numericalresponse>
	
### Variables

Use regular TeX symbols for variables. Do not use `\text{}` for variables, except for subscript labels *consisting of more than one letter*. `d_i` is fine, as is `d_\text{initial}`; do not use `d_{initial}`.

### Multiple Components

Regrettably, edX doesn't (yet) have a good way to put multiple text boxes on the same line, so we can't easily provide vector component input in the convenient `< _ , _ , _ >` style like on WebAssign.

There's a way to jerry-rig a vector component input involving an HTML table, but that renders the problem fragile and the code difficult to read. Instead, just put each component on its own line as its own `numericalresponse` box, and label them with x, y, and z subscripts.