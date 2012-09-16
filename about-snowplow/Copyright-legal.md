## Copyright

SnowPlow is copyright 2012 SnowPlow Analytics Ltd.

Significant portions of the JavaScript tracker `snowplow.js` are copyright 2010 Anthon Pang.

## License

The JavaScript tracker `snowplow.js` is largely based on Anthon Pang's [`piwik.js`] [piwikjs], the JavaScript tracker for the open-source [Piwik] [piwik] project, and is distributed under the same license as `piwik.js`, [Simplified BSD] [bsd].

The rest of the SnowPlow project is licensed under the [Apache License, Version 2.0] [license] (the "License");
you may not use this software except in compliance with the License.

Unless required by applicable law or agreed to in writing, software
distributed under the License is distributed on an "AS IS" BASIS,
WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
See the License for the specific language governing permissions and
limitations under the License.

## Third-party, git-submoduled contributions

The loosely-coupled architecture of SnowPlow makes it easy to swap out individual sub-system implementations for first- or third-party alternatives.

For example, in place of the first-party CloudFront collector, you can use the third-party [SnowCannon] [snowcannon] node.js-based collector. 

The SnowPlow team "endorse" some third-party sub-system implementations by git-submoduling those GitHub repositories into the SnowPlow repository - see the [2-collectors] [2-collectors] folder for an example of this.

Please note that third-party, git-submoduled contributions to SnowPlow remain the copyright their respective authors.

Also be aware that third-party, git-submoduled contributions may be released under a different license to [Apache License, Version 2.0] [license]; please consult the licensing information in their original GitHub repositories for confirmation.

[license]: http://www.apache.org/licenses/LICENSE-2.0
[piwik]: http://piwik.org/
[piwikjs]: https://github.com/piwik/piwik/blob/master/js/piwik.js
[bsd]: http://www.opensource.org/licenses/bsd-license.php
[snowcannon]: https://github.com/shermozle/SnowCannon
[2-collectors]: https://github.com/snowplow/snowplow/tree/master/2-collectors