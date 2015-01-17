# Qhue

Qhue (pronounced 'Q') is an exceedingly thin Python wrapper for the Philips Hue API.

I wrote it because some of the other (excellent) frameworks out there weren't quite keeping up with developments in the API.  Because Qhue encodes almost none of the underlying models - it's really just a way of constructing URLs and HTTP calls - it should inherit any new features automatically.

## Understanding Qhue

Philips, to their credit, created a beautiful RESTful API for the Hue system.  It may not yet have all the features we'd like, but what does exist is very elegantly designed.  

You can (and should) read [the full documentation here](http://www.developers.meethue.com/philips-hue-api), but a quick summary is that resources like lights, scenes and so forth each have a URL, that might look like this:

    http://[myhub]/api/<username>/lights/1

You can read information about light 1 by doing an HTTP GET of this URL, and modify it by doing an HTTP PUT.

The `qhue` module has a Resource class, which represents something that has a URL. By calling an instance of the class, you'll make an HTTP request to the hub.  It also has a Bridge class, which is a handy starting point, and is itself a Resource. 

## Examples

If you have a Resource, you can check its URL:

    # Connect to bridge with userid
    from qhue import Bridge
    b = Bridge("192.168.0.45", 'newdeveloper')
    print b.url   # Just to see what's happening

By getting an attribute of a Resource, you construct a new resource with the attribute name added to the URL:

    lights = b.lights
    print lights.url    # Should have '/lights' on the end
    # Let's actually call the API and print the results
    print lights()  

Qhue takes the JSON that's returned and turns it back into Python objects, typically a dictionary:

    # Get the config info and print the ethernet MAC address
    print b.config()['mac']

We'd like to be able to contruct all our URLs the same way, but you can't use numbers as attribute names, so we can't write, say, `b.lights.1`.  As an alternative, you can use dictionary key syntax:

    # Get information about light 1
    print b.lights[1]()

Alternatively, when you call a resource, you can give it arguments, which will be added to its URL:

    # This is the same as the last example:
    print b('lights', 1)

To make a change to a value, you use a keyword argument.  You can change the brightness and hue of a light by setting properties on its *state*, for example:

    # These are all equivalent:
    b.lights[1].state(bri=128, hue=9000)
    b.lights(1, 'state', bri=128, hue=9000)
    b('lights', 1, 'state', bri=128, hue=9000)

This covers most simple cases.  If you don't have any keyword arguments, the HTTP request will be a GET.  If you do, it will be a PUT.  Sometimes, though, you need to specify a POST or a DELETE, and you can do so with the special *http_method* argument, which will override the above rule:

    # Delete rule 1
    b('rules', 1, http_method='delete')

And, at present, that's about it.  How's that for approx 50 lines of code?


# Prerequisites

This works under Python 2.  It may work under Python 3.  It uses Kenneth Reitz's excellent [requests](http://docs.python-requests.org/en/latest/), so you'll need to:

    pip install requests

or similar before using Qhue.


# Licence

This little snippet is distributed under the GPL v2. See the LICENSE file. (They spell it that way on the other side of the pond.)

# Contributing

Suggestions, patches, pull requests welcome.  There are many ways this could be improved.  If you can do so in a general way, without adding too many lines, that would be even better!  Brevity, as Polonius said, is the soul of wit.

