--- 
wordpress_id: 375
layout: post
title: Optimal web-sized identifiers
wordpress_url: http://serialized.net/?p=375
---
As part of a project I'm working on for fun (shhhh) I've been trying to solve an interesting problem -- what is the most compact way I can uniquely refer to something in a URI?

This one's going to get complicated, so here's the tl;dr version. To get the shortest possible, web-safe identifiers:
<ul>
<li>encode identifiers with base62 or a customized version of base64 that uses valid characters.</li>
<li>Use a big enough hash space to get an acceptable level of collisions.</li>
</ul>

Ok. Here's the long-winded version.

It's a little like the "URL Shortener" use case, where you want to take a big fat link and describe it in a tiny link. In this case, I'm converting individual sentences into a tiny, linkable representation.

So, I need a very short identifier that obeys certain (conflicting) properties:
<ul>
<li>As much as possible, the identifier should be the same regardless of the order that I create the sentences in</li>
<li>It should be as short as possible, because I may need to refer to a lot of these in a single URI</li>
</ul>

In the URL shortener mode, they tend to solve this by just keeping a global "ID" counter and incrementing it every time someone creates a new link. I need to use something a lot more like a hash. (MD5 or SHA1 are typical choices here.)

So what are the constraints?
<ul>
<li>How long can a URI be?</li>
<li>What characters are we allowed to use?</li>
<li>How much unique data is going to be represented?</li>
<li>How tolerant can we be of collisions? (When 2 "objects" might map to the same shortened identifier?)</li>
</ul>

<h3>URI constraints</h3>
The first 2 are simple and global. There's no real standard on URI length, but <a href="http://www.boutell.com/newfaq/misc/urllength.html ">smart people have done the legwork for us</a> so we'll steal their conclusions, and say "keep URI's shorter than 2,000 characters."

As far as the characters that can be used, we can consult <a href="http://tools.ietf.org/html/rfc3986">RFC 3986</a> in the "Unreserved Characters" section and find we're allowed 0-9, a-z, A-Z, and "- _ . ~" (dash, underscore, dot, tilde.) I'm going to avoid using those because I'd like to keep them for separating my identifiers. So, if we just go with the "normal characters", there's 62 of them.

<h3>Collision Constraints</h3>

If you assume a hashing function generates numbers pretty randomly spread throughout the number space available to it (which I will for this) there's actually some pretty good math we can use to figure out how likely a collision will be. If you're interested in where this comes from, check out the <a href="http://en.wikipedia.org/wiki/Birthday_paradox">Birthday Paradox</a>, which is nicely written up at Wikipedia. Using the formulas there you can calculate the probability of having 2 keys that collide, as a function of how many things you're trying to insert, and the size of the overall hash space.

<h3>Putting it all together</h3>

First, figure out how many items you want to be able to store. (for me, 20,000 is a high estimate).
Second, figure out how comfortable you are with having some keys collide. 50/50 odds? (0.5) 1/1000 chance? (0.001)
I'm actually pretty ok having a very small number of collisions -- I'm going to use 0.99, a 99% chance that SOMETHING will collide. (That's still going to be a small number, less than a 100% chance that anything at all will collide, let alone more than one.)

I sketched up a little script for this article (which you can <a href="http://axis.serialized.net/gitweb/?p=utilities.git;a=blob;f=hashspace_model;hb=HEAD">grab from my git repo</a>. It's pretty self-documenting, so I'll let it speak for itself.

The goal is, given our constraints, and the fact that we need to use only the characters 0 .. 9, a .. z, A .. Z, (a) how many of those characters do we need, and (b) given that it probably won't be an exact fit, what will our collision probability end up being?

[perl]
my ($items, $probability) = (0,0);
GetOptions(&quot;items=i&quot; =&gt; \$items, &quot;probability=f&quot; =&gt; \$probability);

printf(&quot;Trying to store %d items with a %0.5f chance of collision\n&quot;, $items, $probability);

# solve the birthday paradox in terms of 'how big the range should be'
# http://en.wikipedia.org/wiki/Birthday_paradox#Calculating_the_probability =&gt; wolfram alpha, 'solve for d'
my $max = -1*(($items - 1)*$items)/(2 * log(1-$probability));

print &quot;To do that, we'd need to be working in the range 1:$max\n&quot;;

# compare that back to the original formula from Wikipedia and make sure that worked
my $calculated_prob = get_probability($max, $items);

printf(&quot;probability %0.10f should match your requested probability of %0.10f, or something went wrong.\n&quot;, $calculated_prob, $probability);

# so it does, cool
# how many bits would it take to represent $max?

my $frac_bits = log($max)/log(2);
my $real_bits = int($frac_bits)+1;

printf(&quot;This can be represented by %0.2f bits (%d)\n&quot;, $frac_bits, $real_bits);

# ok, so how about encoding those into URI's? We can use 0-9, a-z and A-Z, which gives 62 &quot;digits&quot;
# aka base62. $max in base62 can be calculated like we did &quot;how many base2 digits were needed&quot;

my $base62_digits = int(log($max)/log(62))+1;


print &quot;Using 0..9, a..z and A..Z we can represent each object with $base62_digits digits\n&quot;;

# ok, if we're using 5 digits, what's our actual &quot;space&quot; and what's the probability of a collision?

my $newmax = 62**$base62_digits - 1;

# which is actually how many bits?
my $newbits = int(log($newmax)/log(2));

printf(&quot;Given that we'll have to use %d digits, (%d bits), the real probability of a collision is %0.10f\n&quot;, $base62_digits, $newbits, get_probability($newmax, $items));

sub get_probability {
    my($max, $items) = @_;
    return 1 - (($max - 1)/$max)**(($items*($items-1))/2);
}
[/perl]

The most mysterious parts of this are probably the formulas. The one in the get_probability subroutine is transcribed right <a href="http://en.wikipedia.org/wiki/Birthday_paradox#Calculating_the_probability">from the Wikipedia page</a>, but the other one is the same formula, solved for a different value. In general, if you need to do this, <a href="http://wolframalpha.com">WolframAlpha</a> is a math nerd's dream come true. I just asked it to "solve (the equation) for d" and got the new formula I needed.
<a href="http://serialized.net/wp-content/uploads/2010/05/solve_equation.jpg"><img src="http://serialized.net/wp-content/uploads/2010/05/solve_equation.jpg" alt="solve p = 1 - e^((-1*n*(n-1))_2d) for d" title="Solve Equation" width="579" height="379" class="alignnone size-full wp-image-381" /></a>

The solution actually comes from "show your steps" -- I can find an intermediate form that's easier to represent in a non-math-centric programming language. (I'm sure you can do imaginary numbers in perl, but it was kind of outside the scope of my plans for this evening.)

Here's the formula I ended up using:
<a href="http://serialized.net/wp-content/uploads/2010/05/solution.jpg"><img src="http://serialized.net/wp-content/uploads/2010/05/solution.jpg" alt="Solved for D" title="solution" width="107" height="41" class="alignnone size-full wp-image-379" /></a>

Here's a few sample runs of the script:

First, using my personal constraints for this project:
[code]
./hashspace_model -i 20000 -p 0.99
Trying to store 20000 items with a 0.99000 chance of collision
To do that, we'd need to be working in the range 1:43427276.7179157
probability 0.9900000006 should match your requested probability of 0.9900000000, or something went wrong.
This can be represented by 25.37 bits (26)
Using 0..9, a..z and A..Z we can represent each object with 5 digits
Given that we'll have to use 5 digits, (29 bits), the real probability of a collision is 0.1961141788
[/code]

Cool! So I said I'm ok with a 99% chance of a collision, and the algorithm figured out that in order to do that, I'd need to be using 5 digits of base62. And if I'm using 5 digits of base62, I get 3 more bits than I strictly "need", which means I end up with only about a 1/5 chance of EVER getting a collision.

Let's say I wanted to be more strict, and go "one in a million".

[code]
./hashspace_model -i 20000 -p 0.000001
Trying to store 20000 items with a 0.000001 chance of collision
To do that, we'd need to be working in the range 1:199989899999232
probability 0.0000009992 should match your requested probability of 0.0000010000, or something went wrong.
This can be represented by 47.51 bits (48)
Using 0..9, a..z and A..Z we can represent each object with 8 digits
Given that we'll have to use 8 digits, (47 bits), the real probability of a collision is 0.0000009103
[/code]

In this case base62 comes pretty close to exactly the dimensions that we want, so we more or less get 1/1,000,000 on the nose with 8 digits.

If you grab the script to play with, you may need to tweak the printf's and make sure they have enough resolution for the data you're trying to examine.

Ok, so that tells us how bit our bitspace needs to be, but not how to get a hash, or how to do base62.

<h3>Hashing Function</h3>

I chose md5 because.... it seems to work fine. I'm sure there's a better option, but this is working for now. However, md5 has a lot more bits available than I need. How to just steal a few of them?

First, you need to know how many bits you want. Thankfully, I know I want 29 bits (thanks, helper script!). I can extract 29 bits of information from it by making a "mask" of 29 1's, which can be done easily like so:

[perl]
my $mask = 2**29 - 1;
[/perl]

So, now I just need a raw integer slice of an md5, and do a "logical and" of that:

[perl]
use Digest::MD5 qw(md5);
# unpack makes this back into an integer for us
# L == interpret the data as a 32 bit unsigned long. 
# See 'perldoc -f pack' for a ton of other options
my $value = unpack(&quot;L&quot;, md5(&quot;the string we want&quot;));
$value = $value &amp; $mask;
[/perl]

Groovy. Now we know what number we want to represent, we need to actually represent that in this weird "base62" format.

The algorithm for converting to base(anything) is actually pretty easy.
We're used to thinking in base10, so I'll show an example of running the algorithm to from and to base10, just so the flow is clear.

<table>
<tr><td>125</td><td>Starting Value</td></tr>
<tr><td>125 % 10 = 5</td><td>Find the remainder when dividing by the "destination base." Keep "5" as the "new number"</td></tr>
<tr><td>125 / 10 = 12</td><td>Divide by the "destination base". This chops the number off the end that we just snagged as a remainder</td></tr>
<tr><td>12 % 10 = 2</td><td>Do it again with the result of the division. Stick the result to the front of number we're keeping track of (now 25)</td></tr>
<tr><td>12 / 10 = 1</td><td>And do the division again</td></tr>
<tr><td>1 % 10 = 1</td><td>Last remainder, glue to the front again, and we have the answer "125". (Back where we started.)</td></tr>
<tr><td>1 / 10 = 0</td><td>As soon as we get 0 from the division, our work here is done.</td></tr>
</table>

Simple enough to understand in base10, but the exact same technique works when going from base10 to any other base (2 would work to convert to binary, we're using 62, and if you could find enough characters, you could go to something crazy like base300 using the same idea.)

Here's the source for it:

[perl]
# even though they are letters we are using them here in the role of &quot;digits&quot;
my @digits = (0 .. 9, 'a' .. 'z', 'A' .. 'Z');
my %digits = map { $digits[$_] =&gt; $_ } 0 .. $#digits;

sub to_base {
    my($base, $value) = @_;
    die &quot;base $base out of range 1-62&quot; unless ($base &gt; 0 &amp;&amp; $base &lt;= 62);
    return $digits[0] if $value == 0;
    my $rep = &quot;&quot;;

    while($value &gt; 0) {
        # prepend the &quot;digit&quot; we get when dividing by the base        
        $rep = $digits[$value % $base ] . $rep;
        # then &quot;shift right&quot; the working value
        $value = int( $value / $base );
    }
    return $rep;
}

sub from_base {
    my($base, $rep) = @_;
    die &quot;base $base out of range 1-62&quot; unless ($base &gt; 0 &amp;&amp; $base &lt;= 62);
    my $value = 0;
    # this pattern grabs a character at a time, left to right
    for ( $rep =~ /./g ) {
        $value *= $base;
        $value += $digits{$_};
    }
    return $value;
}
[/perl]

So there you have it. Provably optimal URI-compatible identifiers with 3 easy steps:
<ol>
<li>Figure out what the constraints for your problem space are</li>
<li>Grab enough bits from md5</li>
<li>Convert to (and from) base62</li>
</ol>
