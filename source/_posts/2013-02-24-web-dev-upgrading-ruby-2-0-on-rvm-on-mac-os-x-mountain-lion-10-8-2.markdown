---
layout: post
title: "Web Dev: Upgrading Ruby 2.0 on RVM on Mac OS X Mountain Lion 10.8.2"
date: 2013-02-24 11:27
comments: true
categories: [web development, ruby on rails, ruby, ruby 2.0, rvm]
---

{% img http://rickypai.s3.amazonaws.com/blog/2013/photo-a-day/2013-02-24/ruby-400.png 200 %}

Around 1AM PST last night, [Ruby 2.0.0-p0](http://www.ruby-lang.org/en/news/2013/02/24/ruby-2-0-0-p0-is-released/) was released with goodies like default support of UTF-8, symbol list creation, binary search in arrays, and many background optimizations. What I am quite excited about is a new experimental feature called Refinements to override method within a specific context only.

Upon discovering the news at about 3AM last night, I decided to upgrade before I sleep. This may have been my worst decision of the night.

Here is the list of commands to use to upgrade your rvm with ruby 2.0.0-p0 with the least number of surprises. We will talk about surprises later:

Update to the latest RVM:<br>
`rvm get head`

Install requirements:<br>
`rvm requirements run`

Install OpenSSL:<br>
`rvm pkg install openssl`

Install Ruby 2.0.0-p0 with OpenSSL:<br>
`rvm install 2.0.0 --with-openssl-dir=$rvm_path/usr`

Tell RVM to use Ruby 2.0.0:<br>
`rvm use 2.0.0`

Confirm you have Ruby 2.0.0:<br>
`rvm current`
`ruby --version`

The first test is goto your Rails project root and run bundler:<br>
`bundle`

It is most likely you will get this error:<br>
`Bundler is not compatible with Ruby 2.0.`
`Please upgrade to Bundler 1.3 or higher.`

Unfortunately, if you run `gem update bundler`, the newest version is only bundler-1.2.4.

You need to install the prereleased bundler-1.3.0 branch, currently 1.3.0.pre.8:<br>
`gem install bundler --pre`

Should you encounter conflicts with older versions of bundler:<br>
`gem uninstall bundler`

and select the older version to uninstall.

Now run bundle again, and everything should work right!

{% img http://rickypai.s3.amazonaws.com/blog/2013/photo-a-day/2013-02-24/ruby2finally.png 570 %}
Horray!

If you encounter this error in other terminal windows:<br>
`ERROR: Gem bundler is not installed, run 'gem install bundler' first.`

Run:<br>
`rvm reload`<br>

<br><hr>

Now if your installation wasn't as smooth as what was described above, here are some side notes:

When installing the requirements:<br>
`rvm requirements run`

Should you run into permission problems, most likely you had the same problem as me, which was not having the right permission to modify contents in `~/.rvm` as my system drive was migrated from a previous laptop. (Tip: `sudo rvm requirements run` won't help. I discovered this the hard way.)

The quickest fix I found was just to delete RVM and reinstalling it.

Remove RVM:<br>
`sudo rm -rf ~/.rvm`

Install RVM with MRI:<br>
`curl -L https://get.rvm.io | bash -s stable --ruby`

Alternatively, install RVM with MRI and Rails:<br>
`curl -L https://get.rvm.io | bash -s stable --rails`

<br><hr>

Additional Tip:

With the new version of Ruby, RVM will create a new gemset and install new gems. When this happens, it will generate RDoc and RI pages, which can take up quite some time.

If you don't need those documentations (I always look from the web or github wiki pages), here is what you can do to speed things up by skipping them:

Create or modify `~/.gemrc` and add the following line:<br>
`gem: --no-rdoc --no-ri`

Courtesy of [ruby on rails - Using --no-rdoc and --no-ri with bundler - Stack Overflow](http://stackoverflow.com/questions/7535737/using-no-rdoc-and-no-ri-with-bundler)

Have fun with Ruby 2.0!

If you have any questions, feel free to find me at @[rickypaipie](https://twitter.com/rickypaipie). Sorry that I haven't implemented a comments section for this blog yet.