This is a page of hints, tips and explanations to help you work with SnowPlow. If something looks like a bug in SnowPlow but isn't, it will end up on this page too.

1. [Why are browser features missing in IE?](#ie-features)
2. [Ruby problem: You have already activated xxx 0.2...](#gem-activation)

<a name="ie-features"/>
### Why are browser features all recorded as null for Internet Explorer?

With the exceptions of cookies and Java, our JavaScript tracker cannot detect what browser features (PDF, Flash etc) a given instance of Internet Explorer has. This is because IE, unlike the other major browsers, does not populate the `window.navigator.mimeTypes[]` and `navigator.plugins[]` properties. There are other ways of detecting some browser features (via ActiveX), but these are not advised as they can trigger UAC warnings on Windows.

<a name="gem-activation"/>
### Ruby problem: You have already activated xxx 0.2, but your Gemfile requires xxx 0.1

The recommended procedure for Ruby gems which are apps (not libraries) is to bundle the `Gemfile.lock` file with your app. You can see the `Gemfile.lock` files for our Ruby apps here:

* https://github.com/snowplow/snowplow/blob/master/4-storage/storage-loader/Gemfile.lock
* https://github.com/snowplow/snowplow/blob/master/3-etl/emr-etl-runner/Gemfile.lock

The problem is if there is a clash between the Ruby gem dependencies specified in the app's `Gemfile.lock` and Ruby gems installed by other apps on your system. There is an 'ugly hack' fix, and a proper solution:

1. **'Ugly hack' fix:** edit the `Gemfile.lock` file and bump the dependency to the version already installed
2. **Proper solution:** use [RVM (Ruby Version Manager)] [rvm] with a [project-specific rvmrc file] [rvmrc]

Documenting the rvmrc-based setup in our setup guide is on our todo-list.

[rvm]: https://rvm.io/
[rvmrc]: https://rvm.io/workflow/rvmrc/
