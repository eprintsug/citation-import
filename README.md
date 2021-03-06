Citation Count Dataset and Import Plug-ins for GNU EPrints 3
============================================================

These scripts and plug-ins enable an EPrints repository to retrieve and store
citation counts from Scopus, Web of Science and Google Scholar.

Your institution requires a subscription to Scopus and Web of Science in order
to use the plug-ins for their services. Google Scholar is freely accessible,
but requests that robots do not extract data from its web site (so you should
only use the plug-in to conduct searches triggered manually). See the web sites
listed below for the details for each service.

### Scopus

  The Scopus plug-in uses the Scopus Document Search API (the XML web service
  that underpins the JavaScript APIs). Your institution requires a developer
  key for the API. See https://www.developers.elsevier.com/cms/index.

### Web of Science:

  The Web of Science plug-in uses Web of Science Web Services Premium.
  Access to Web Services Premium is automatic once you are subscribed to Web
  of Science and InCites. Note: Web Services Lite is not supported by the
  plugins as it no longer provides citation data.

### Google Scholar (NOT SUPPORTED):

  The Google Scholar plug-in is a screen-scraper based on the
  Export::GScholar plug-in shipped with EPrints 3.2. See
  http://scholar.google.com/intl/en/scholar/about,html. Google's full
  terms of use are at http://www.google.com/accounts/TOS. This plugin
  is not actively maintained.


Installation
------------

Before using the import plug-ins, you need to install the following
from CPAN:

* `Unicode::Normalize` (for Scopus)
* `SOAP::Lite` (for Web of Science)
* `WWW::Mechanize::Sleepy` (for Google Scholar)
* `URI`. It is recommended that you upgrade the URI package by
updating the one packaged with EPrints in *perl_lib* or by deleting that
one and using the latest from CPAN. *NOTE:* ensure that `URI::OpenURL`
remains installed.
<del>
1.  Copy all of the files in the *eprints* sub-directory into the root directory
    of your EPrints installation, e.g. `cp -R eprints/* /opt/eprints3`
</del>
<del>
2.  Copy all of the files in the *archives* sub-directory into the configuration
    folder of the archive into which you want to import citation data, e.g.
    `cp -R archives/* /opt/eprints3/archives/foobar`
</del>
<del>
3.  Go to the *cfg.d* directory of your archive.

    1.  Move the contents of *eprint_fields.pl.inc* into your *eprints_fields.pl*
    2.  Move the contents of *datasets.pl.inc* into your *datasets.pl*
    3.  Insert your Scopus developer key into *scapi.pl*
    4.  Check the options in *scapi.pl*, *wos.pl* and *gscholar.pl*
    5.  If you want to view and manipulate citation objects via the "Manage
        Records" interface, add the lines "+citation/view" and
        "+citation/destroy" the appropriate user roles in *user_roles.pl*
</del>
4.  Update the database structure of your archive to include the new dataset
    and fields, e.g. `epadmin update foobar`

You may also want to look through the import plug-ins themselves and ensure
that their use of eprint types and fields is suitable for your archive.


Using the Plug-ins
------------------

You can import citation data into your database using the *import_citationdata*
and *update_citationdata* scripts.

*import_citationdata* requests data for a given list of eprint IDs.

*update_citationdata* is intended to be run as a daily or hourly cron job, and
implements a schedule that requests data for every eprint once per month.

See the Perl documentation for each script for details on how to invoke the
scripts, i.e. use `perldoc /opt/eprints3/bin/import_citationdata`.

All citation data is stored in a dataset called *citation*. The most recent
counts for any given eprint are cached in the *scopus*, *wos* and *gscholar*
fields of that eprint. E.g. to display Scopus' citation count on an eprint's
summary page, put `<epc:print expr="scopus_impact"/>` in the appropriate place
of your *summary_page.xml*.


Support
-------

For general support enquiries please use the EPrints Tech List at
http://www.eprints.org/tech.php/

Bugs and and feature requests can be logged in GitHub.


Contributors
------------

The citation count import plug-ins were developed by the Digital Repository
Team at the Queensland University of Technology in Brisbane, Australia.
For more about us, see http://www.digitalrepository.qut.edu.au.

Developers:

*  Nicholas Sheppard <nicholas.sheppard@qut.edu.au>
*  Mark Gregson <mark.gregson@qut.edu.au>

Updates:
*  Sebastien Francois <sf2@ecs.soton.ac.uk>


Copyright
---------

Copyright (c) 2011 Queensland University of Technology, Queensland, Australia

The plug-ins are free software; you can redistribute them and/or modify
them under the terms of the GNU General Public License as published by
the Free Software Foundation; either version 2 of the License, or
(at your option) any later version.

The plug-ins are distributed in the hope that they will be useful,
but WITHOUT ANY WARRANTY; without even the implied warranty of
MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
GNU General Public License for more details.

You should have received a copy of the GNU General Public License
along with EPrints 3; if not, write to the Free Software
Foundation, Inc., 59 Temple Place, Suite 330, Boston, MA  02111-1307  USA

Changelog
---------

- May-June 2013 (MrkGrgsn w/sf2)

    * Refactored `process_eprints()` to improve the abstract-concrete
      class interaction.
    * bin script: removed `parse_retry` functionality
    * Scopus: added query fallbacks to Scopus - EID to DOI to metadata -
      improves match success rate and handle changed EIDs.
    * Scopus: corrected encoding of multi-byte characters in the query
      string w/URI 1.60
    * Scopus: added a call() method to Scopus to perform HTTP requests
      with retry on transport failure
    * Scopus: added a workaround for Scopus return malformed XML error
      responses
    * Scopus: refined metadata querystring generation to handle more
      special characters
    * WoS: configured Web Services Premium by default (Lite doesn't
      work)
    * WoS: added DOI filtering to remove ones with mismatched
      parenthesis that WoS can't interpret
    * WoS: fixed a character escaping issue in the userQuery element
    * WoS: modified `get_session()` and dispose() to use `$plugin->call()`
    * WoS: `get_uri_for_eprint()` now links direct to the record via the
      WoS OpenURL URI

- May 2013 (sf2)

    * Scopus: don't fail if an EPrint has no title
    * WoS: if an EPrint's UT has changed, re-do a search instead of die()'ing

- December 2012 (sf2)

    * WoS: added support for v3 of the API
    * WoS: added restrictions for the maximum number of requests per seconds (2)
      and per session (10,000)
    * WoS: Unicode patch for queries
    * Scopus: support for updated API / End-points
    * bin script: if 'filename' is omitted, the script will process all the items
      in the live archive


