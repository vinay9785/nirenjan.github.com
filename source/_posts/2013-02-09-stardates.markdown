---
layout: post
title: "Stardates"
date: 2013-02-09 19:41
comments: true
categories: [Code, Ruby, Octopress]
---

Stardates are common usage in the Star Trek universe. You'd easily hear Captain
Picard talking in the intro scene, "Captain's log, stardate 41325.9". These
combinations of numbers are meant to give the viewer a sense of the future,
without explicitly stating the exact timeframe in which the show occurs. Of
course, we know that Star Trek: The Next Generation is set in the 24th century,
but that's beside the point.

<!-- more -->

Here, I'm going to talk about the stardates shown in the header of the posts.
These are meant to give me some semblance of futuristic activity, however, they
are tied to very real and very current dates. In the field of astronomy,
observations are recorded using a number format similar to stardates, however,
these are called [Julian days](http://en.wikipedia.org/wiki/Julian_day). In
short, like Unix time is the number of seconds since the Unix epoch on January
1st, 1970, midnight GMT, Julian days are the number of days since the epoch at
noon GMT on January 1st, 4713 BC in the [Proleptic Gregorian
Calendar](http://en.wikipedia.org/wiki/Proleptic_Julian_calendar). The current
Julian day, as of this writing is 2456333. Most observations record the time
with an additional degree of precision by adding a fractional portion of the
day. So, for example, it's now 2456333.6.

Obviously, one could take this and extend it ad infinitum, but we're not going
to do that. For the purpose of this blog, it's quite sufficient to maintain time
precision of about a quarter hour. In addition, it's not necessary to display
the full Julian day, but truncate the most significant two digits. That, is, I'd
display the current time as 56333.58 (Feb 9th, 2013, 17:59 PST).

This is easily done in Ruby with the following code:
{% codeblock Stardate lang:ruby %}
t = Time.now
s = "%5.2f" % (t.strftime("%s") / 86400 + 40587.5)
{% endcodeblock %}

The `"%5.2f"` syntax should be straightforward to anybody using Python or Ruby,
as this is the way (or at least the one I know of), to use printf style format
specifiers without actually using printf's. The `t.strftime{"%s"}` returns the
current Unix timestamp, and divinding by 86400 gives us the number of days since
the Unix epoch. The last portion, `+ 40587.5`, gives us the number to add to
get the current Julian day, or fraction thereof. You see, 2440587.5 is the
Julian day timestamp at midnight GMT on January 1st, 1970. So, by adding the
number of days since the epoch to 40587.5, I get exactly what I want, the
current Julian day (minus 2400000, of course).

The astute observer would see that this is not quite perfect, I'd get a rollover
when the counter reaches 99999.99. Sure, but I'm not too worried about that. The
rollover will happen on August 31st, 2132 at noon GMT (less about 7 minutes,
accounting for the rounding up by the printf function), which is well beyond my
lifetime, and by which point, we may even be using some other form of
timekeeping other than the Julian calendar.

The even more astute observer would notice that this calculation doesn't account
for leap seconds. Again, I don't care about them. The rounding off means that a
second here or there doesn't matter; each 100th of a day is 864 seconds, or 14
minutes, and 24 seconds (just under a quarter of an hour).

Now, how do we get this to work in Octopress? The syntax is straightforward, we
can tweak the date plugin as shown below.
{% codeblock Date plugin changes lang:diff %}
diff --git a/plugins/date.rb b/plugins/date.rb
index 49fb79a..af660df 100644
--- a/plugins/date.rb
+++ b/plugins/date.rb
@@ -29,15 +29,27 @@ module Octopress
       end
     end
 
+    def stardateize(date)
+      date = datetime(date)
+      "Stardate #{stardate(date.strftime('%s').to_f)}"
+    end
+
+    def stardate(seconds)
+      "%5.2f" % (seconds / 86400 + 40587.5)
+    end
+
     # Formats date either as ordinal or by given date format
     # Adds %o as ordinal representation of the day
     def format_date(date, format)
       date = datetime(date)
       if format.nil? || format.empty? || format == "ordinal"
         date_formatted = ordinalize(date)
+      elsif format == "stardate"
+        date_formatted = stardateize(date)
       else
         date_formatted = date.strftime(format)
         date_formatted.gsub!(/%o/, ordinal(date.strftime('%e').to_i))
+        date_formatted.gsub!(/%J/, stardate(date.strftime('%s').to_f))
       end
       date_formatted
     end
{% endcodeblock %}

Like the original date plugin uses the `%o` syntax to generate ordinal
representations of days, the stardate representation uses the `%J` syntax. Since
neither of these are in use by strftime, we can repurpose them for our own use.
