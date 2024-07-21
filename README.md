[Great Southern Bank](https://www.greatsouthernbank.com.au/), [formerly known as Credit Union Australia](https://www.greatsouthernbank.com.au/about/news/blog/media-releases/2021/feb/cua-to-rebrand-as-a-bank-in-its-75th-year), is one of the biggest member-owned banks / credit unions in Australia. Unfortunately, the only way it seems possible to get usable data files with account and transaction information out of their system is to use their web banking user interface. It has a bunch of protections in it to prevent links on any page working beyond the single render of that page at that point in time, and also uses a whole bunch of iframes that's a bit gross. But anyway. In search of a more reliable way to get this data for myself in a more automated manner (and without poking proprietary banking APIs that might lock me out of my own accounts), I sat down and worked out a whole bunch of Selenium commands that will click the right buttons to trigger the UI in the way required to:
* choose the account to export (i.e. go to its transaction history page)
* choose the "6 months" transaction history window, the widest one GSB/CUA seems to offer
* click the right export button in the right dropdown of the UI to kick off the CSV export

## Setup + Usage instructions

* Install Jupyter Notebook
* Instal Ruby and the `selenium-webdriver` gem. I developed this notebook on Ubuntu 20.04's system Ruby 2.7 and `ruby-selenium-webdriver` APT package which is apparently version `3.142.7`.
* Install the `IRuby` kernel - `gem install --user-install iruby` (see the [IRuby GitHub](https://github.com/SciRuby/iruby/blob/master/README.md#installation) for reference)
* Run the notebook cell-by-cell. You will see an instance of Firefox start up on your computer that you can follow all the actions in. The notebook code contains prompts for entering your login credentials and choosing the accounts to trigger an export for.

## TODO

* Detect when the CSV export's download is complete and/or in-progress
    * This will require some kind of wranging of the Firefox downloads UI. Might need Selenium 4+ to do this via the "context" thingo (`context = "chrome" - see https://firefox-source-docs.mozilla.org/python/marionette_driver.html#marionette_driver.marionette.Marionette), which can facilitate running scripts in the context of the Firefox UI ("chrome") itself. There also seems to be some kind of JS-level APIs for interacting with the Downloads stuff in Firefox - see https://searchfox.org/mozilla-central/source/toolkit/components/downloads/Downloads.sys.mjs
* Grab and rename the downloaded files to include account details.
    * GSB/CUA serve up the CSV export files with the annoyingly generic filename of `<your_account_id>_<current_date>.csv`. This doesn't tell you what account the export came from.
    * Reliably finding the created file might be a challenge - filesystem event APIs? continuous polling of the directory? Ideally the filename could just be pulled out of Firefox itself - this would require the same kind of infrastructure as detailled above though.
* Turn this into some kind of proper script that can be fed credentials in a secure manner so it can be called from `cron` or something like that, so I can stop ending up with gaps in my copies of my transaction history when I forget to run an export for more then 6 months.
