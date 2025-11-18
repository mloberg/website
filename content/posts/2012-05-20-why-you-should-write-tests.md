+++
title = 'Why You Should Write Tests'
categories = ['devops']
proof = false
+++
Last week I started writing tests for my [framework](https://github.com/ivorisoutdoors/Tea-Fueled-Does). I've got to be honest, I rarely write tests for my code. This was the first time I really wrote tests, but I recommend it.

#### It's Easy

I was always under the impression that writing tests for code was complex. After writing some tests for [PHPUnit](http://www.phpunit.de/manual/3.6/en/index.html), I was wrong. The class extends *PHPUnit_Framework_TestCase* and you've got a bunch of assertions methods. For example take a look at this code I've pulled right from my tests.

{{< highlight php >}}
<?php

    require 'test.bootstrap.php';

    use TFD\Auth;

    class AuthTest extends PHPUnit_Framework_TestCase {

        /**
         * Test Auth::login method.
         */

        public function testLogin() {
            $fingerprint = Auth::login('username', 'secret');
            $this->assertInternalType('string', $fingerprint);

            return $fingerprint;
        }

        /**
         * Test Auth::valid method.
         *
         * @depends testLogin
         */

        public function testValid($fingerprint) {
            $this->assertTrue(Auth::valid($fingerprint, 'username', 'secret'));
        }

    }
{{< /highlight >}}

That took me less then ten minutes to write.

#### There's No Guessing

When you write tests, you are making sure that something is working how it's suppose to. There's no guessing if it's going to work. You write a test, if it passes, the code works, if it fails, there's something wrong. When you change something you can verify that it still works by running the tests again.

#### It Automates Testing

Normally when I write code, I make sure it does what it's supposed to do and then forget about it. For example with Tea-Fueled Does, I would create a route or add something to a view to make sure something's working and then delete it. I would never test it again. With tests you can rerun those tests, and with PHPUnit, you can run all your tests in a single command.

#### It Creates Quality Code

How many times have you released something with bugs in it? I've done a couple commits where I find out later, I have a bug in it. Writing tests helps you prevent that.

[Test-Driven Development](http://en.wikipedia.org/wiki/Test-driven_development) is also a great development process. You write a test for a new function/method or an improvement and fix the code until it passes. This creates concise code.

#### Getting Started

[PHPUnit's docs](http://www.phpunit.de/manual/3.6/en/index.html) are a great place to start. NetTuts's also has an [article](http://net.tutsplus.com/tutorials/php/the-newbies-guide-to-test-driven-development/) on it SimpleTest, another PHP test suite.
