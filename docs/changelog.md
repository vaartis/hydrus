---
title: Changelog
---

# changelog

!!! note
    This is the new changelog, only the most recent builds. For all versions, see the [old changelog](old_changelog.html).

## [Version 518](https://github.com/hydrusnetwork/hydrus/releases/tag/v518)

### autocomplete improvements

* tl;dr: I went through the whole tag autocomplete search pipeline, cleaned out the cruft, and made the pre-fetch results more sensible. searching for tags on thumbnails isn't horrible any more!
* -
* when you type a tag search, either in search or edit autocomplete contexts, and it needs to spend some time reading from the database, the search now always does the 'exact match' search first on what you typed. if you type in 'cat', it will show 'cat' and 'species:cat' and 'character:cat' and anything else that matches 'cat' exactly, with counts, and easy to select, while you are waiting for the full autocomplete results to come back
* in edit contexts, this exact-matching pre-fetch results here now include sibling suggestions, even if the results have no count
* in edit contexts, the full results should more reliably include sibling suggestions, including those with no count. in some situations ('all known tags'), there may be too many siblings, so let me know!
* the main predicate sorting method now sorts by string secondarily, stabilising the sort between same-count preds
* when the results list transitions from pre-fetch results to full results, your current selection is now preserved!!! selecting and then hitting enter right when the full results come in should be safe now!
* when you type on a set of full results and it quickly filters down on the results cache to a smaller result, it now preserves selection. I'm not sure how totally useful this will be, but I did it anyway. hitting backspace and filtering 'up' will reset selection
* when you search for tags on a page of thumbnails, you should now get some early results super fast! these results are lacking sibling data and will be replaced with the better answer soon after, but if you want something simple, they'll work! no more waiting ages for anything on thumbnail tag searches!
* fixed an issue where the edit autocomplete was not caching results properly when you had the 'unnamespaced input gives (any namespace) wildcard results' option on
* the different loading states of autocomplete all now have clear 'loading...' labels, and each label is a little different based on what it is doing, like 'loading sibling data...'
* I generally cleared out jank. as the results move from one type to another, or as they filter down as you type, they _should_ flicker less
* added a new gui debug mode to force a three second delay on all autocomplete database jobs, to help simulate slow searches and play with the above
* NOTE: autocomplete has a heap of weird options under _tags->manage tag display and search_. I'm really happy with the above changes, but I messed around with the result injection rules, so I may have broken one of the combinations of wildcard rules here. let me know how you get on and I'll fix anything that I busted.

### pympler

* hydrus now optionally uses 'pympler', a python memory profiling library. for now, it replaces my old python gc (garbage collection) summarising commands under _help->debug->memory actions_, and gives much nicer formatting and now various estimates of actual memory use. this is a first version that mostly just replicates old behaviour, but I added a 'spam a more accurate total mem size of all the Qt widgets' in there too. I will keep developing this in future. we should be able to track some memory leaks better in future
* pympler is now in all the requirements.txts, so if you run from source and want to play with it, please reinstall your venv and you'll be sorted. _help->about_ says whether you have it or not

### misc

* the system:time predicates now allow you to specify the hh:mm time on the calendar control. if needed, you can now easily search for files viewed between 10pm-11:30pm yesterday. all existing 'date' system predicates will update to midnight. if you are a time-search nerd, note this changes the precision of existing time predicates--previously they searched _before/after_ the given date, but now they search including the given date, pivoting around the minute (default: 0:00am) rather than the integer calendar day! 'same day as' remains the same, though--midnight to midnight of the given calendar day
* if hydrus has previously initial-booted without mpv available and so set the media view options for video/animations/audio to 'show with native viewer', and you then boot with mpv available, hydrus now sets your view options to use mpv and gives a popup saying so. trying to get mpv to work should be a bit easier to test now, since it'll popup and fix itself as soon as you get it working, and people who never realised it was missing and fix it accidentally will now get sorted without having to do anything extra
* made some small speed and memory optimisations to content processing for busy clients with large sessions, particularly those with large collect-by'd pages
* also boosted the speed of the content update pipeline as it consults which files are affected by which update object
* the migrate tags dialog now lets you filter the tag source by pending only on tag repositories
* cleaned up some calendar/time code
* updated the Client API help on how Hydrus-Client-API-Access-Key works in GET vs POST arguments
* patched the legacy use of 'service_names_to_tags' in `/add_urls/add_url` in the client api. this parameter is more obsolete than the other legacy names (it got renamed a while ago to 'service_names_to_additional_tags'), but I'm supporting it again, just for a bit, for Hydrus Companion users stuck on an older version. sorry for the trouble here, this missed my legacy checks!

### windows mpv test

* hey, if you are an advanced windows user and want to run a test for me, please rename your mpv-2.dll to .old and then get this https://sourceforge.net/projects/mpv-player-windows/files/libmpv/mpv-dev-x86_64-20230212-git-a40958c.7z/download . extract the libmpv-2.dll and rename it to mpv-2.dll. does it work for you, showing api v2.1 in _help->about_? are you running the built windows release, or from source? it runs great for me from source, but I'd like to get a wider canvas before I update it for everyone. if it doesn't work, then delete the new dll and rename the .old back, and then let me know your windows version etc.., thank you!

## [Version 517](https://github.com/hydrusnetwork/hydrus/releases/tag/v517)

### misc

* thanks to a user, export folders finally support exporting to symlinks!
* if a symlink export-create fails on Windows, the error now tells you to try again in 'run as Admin' mode--seems like this is needed in Win 10+ unless you mess with Group Policy Editor
* 'related tags' should no longer suggest sibling ideals or parents of existing tags! I think!
* when a thumbnail fails to load, the error popup now has a button to open the specific problem-causing file in a new page
* generation of video thumbnails is faster, should fail less in odd cases, and when it completely fails, it now gives the hydrus icon as a final fallback
* generation of image thumbnails now falls back to the hydrus icon as a final fallback
* I think I fixed a focus logic problem where the autocomplete dropdowns on the duplicate filter page would hide if you clicked a results/favourites tab or greyspace
* fixed an error when seeking an mpv video while the video was loading or unloading
* the max 'nullification period' (after which uploads to a hydrus repository are anonymised) is raised from 1 year to 5 (needs server and client update to work)

### transparency and duplicate filter

* two new options, under _media_ and _duplicates_, now control if you would like transpararency-having images to have a checkerboard background rather than the normal media canvas background! you can have it on all the time or just under the duplicate filter. it uses the same style of grid as MPV
* I have a plan for proper native (non-MPV) transparency for gifs and apng, but I think I'll wait for an imagemagick plugin I am planning first
* if you have a white/black media viewer background and prefer not to use the checkerboard, the duplicate filter can now adjust the background colour, either lighter or darker, for both A and B of the pair. altering A as well exposes truly transparent-having images vs ones with opaque white/black fill, which will otherwise blend into a purely white/black background colour. these options are available in the options dialog and the duplicate filter right-hand hover window cog button
* the native image window, embed button, and animation window (with PIL gif rendering) now all adjust their background colour to any odd changes like the duplicate filter's A/B lighten/darken adjustment

### boring cleanup

* cleaned up how popup file buttons are set and cleared
* cleaned up how popup main and secondary texts are set and cleared
* misc linting cleanup

## [Version 516](https://github.com/hydrusnetwork/hydrus/releases/tag/v516)

### misc

* the 'manage sidecar routers' control, which is on manage import folders, manage export folders, path-tagging-before-manual-import, and manual export files, now has import/export/duplicate buttons. you can save and transfer your work now! if you try to import 'export to sidecar' routers to an 'import from sidecar' context or _vice versa_, it should give you a nicely worded error
* fixed the error that was raising when you turn related tags off with the suggestions set to side-by-side layout. very sorry for the trouble!
* apngs that are set to 'loop x times' (usually once) now only loop that many times, on both mpv and my native renderer! like gifs, the 'always loop animations' setting under _options->media_ overrides it!
* fixed an issue with my native renderer not updating on scanbar scrubs very well. should be back to nice smooth instant draw as you scrub
* thanks to a user, folded in another deviant art parser update to the defaults
* updated the setuptools version in the requirements.txt due to a security note--I don't think the problem (which was about some vulnerable regex when fetching malicious package info) applies to us, but running from source users might like to run setup_venv again this week anyway

### related tags

* a new 'concurrence threshold' setting under _options->tag suggestions_ allows you to set how 'strict' the related tags search is. a higher percentage causes fewer but more relevant results. I'm increasing the default this week from 4% to 6%
* two new 'namespace to weight' settings under _options->tag suggestions_ now manage how much weight the 'search' and 'suggestion' sides of related tags have. you can say 'rank the suggestions from character tags highly' or 'rank unnamespaced suggestions lower', and 'do not search x tags' and 'do not suggest y tags'. I have prepped it with some 'creator/character/series namespaces are better than unnamespaced, and title/filename/page/chapter/volume are useless' defaults, but feel free to play around with it
* the related tags algorithm takes a larger sample now, resulting in a _little_ less ranking-variability

### client api

* changed and fixed an issue in the client api's new `get_file_relationships` call. previously, I said 'king' would be null if it was not on the given file domain, but this was not working correctly--it was giving pseudorandom 'fallback' kings. now it always gives the king, no matter what! a new param, `king_is_on_file_domain` says whether the king is on the given domain. `king_is_local` says whether the king is available on disk
* added some discussion and a list of the 8 possible 'better than' and 'same quality' logical combinations to the `set_file_relationships` help so you can see how group merge involving non-kings works
* client api is now version 42

## [Version 515](https://github.com/hydrusnetwork/hydrus/releases/tag/v515)

### related tags

* I worked on last week's related tags algorithm test, bringing it up to usable standard. the old buttons now use the new algorithm exclusively. all users now get 'related tags' showing in manage tags by default (if you don't like it, you can turn it off under _options->tag suggestions_)
* the new algorithm has new cancel tech and does a 'work for 600ms' kind of deal, like the old system, and the last-minute blocks from last week are gone--it will search as much as it has time for, including partial results. it also won't lag you out for thirty seconds (unless you tell it to in the options). it searches tags with low count first, so don't worry if it doesn't get to everything--'1girl' usually doesn't have a huge amount extra to offer once everything else has run
* it also uses 'hydev actually thought about this' statistical sampling tech to work massively faster on larger-count tags at the cost of some variance in rank and the odd false positive (considered sufficiently related when it actually shouldn't meet the threshold) nearer the bottom end of the tags result list
* rather than 'new 1' and 'new 2', there is now an on/off button for searching your local files or all known files on tag repositories. 'all known files' = great results, but very slow, which the tooltip explains
* there's also a new status label that will tell you when it is searching and how well the search went (e.g. '12/51 tags searched fully in 459ms')
* I also added the 'quick' search button back in, since we can now repeat searches for just selections of tags
* I fixed a couple typos in the algorthim that were messing some results
* in the manage tags dialog, if you have the suggested tag panels 'side-to-side', they now go in named boxes
* in the manage tags dialog, if you have suggested tag panels in a notebook, 'related tags' will only refresh its search on a media change event (including dialog initialisation) when it is the selected page. it won't lag you from the background!
* options->tag suggestions now lets you pick which notebook'd tag suggestions page you want to show by default. this defaults to 'related'
* I have more plans here. these related tags results are very cachable, so that's an obvious next step to speed up results, and when I have done some other long-term tag improvements elsewhere in the program, I'll be able to quickly filter out unhelpful sibling and parent suggestions. more immediately, I think we'll want some options for namespace weighting (e.g. 'series:' tags' suggestions could have higher rank than 'smile'), so we can tune things a bit

### misc

* the 'open externally' canvas widget, which shows any available thumbnail of the flash or psd or whatever, now sizes itself correctly and draws the thumbnail nicely if you set the new thumbnail supersampling option to >100%. if your thumbnail is the wrong size (and probably in a queue to be regenerated soon), I _think_ it'll still make the window too big/small, but it'll draw the thumbnail to fit
* if a tag content update comes in with an invalid tag (such as could happen with sidecars recently), the client now heals better. the bad tag is corrected live in more places, and this should be propagated to the UI. if you got a warning about 'you have invalid tags in view' recently but running the routine found no problems, please reboot, and I think you'll be fixed. I'm pretty sure the database wasn't being damaged at all here (it has cleaning safeguards, so it _shouldn't_ be possible to actually save bad tags)--it was just a thing to do with the UI not being told of the cleaned tag, and it shouldn't happen again. thank you for the reports! (issue #1324)
* export folders and the file maintenance dialog no longer apply the implicit system:limit (defaults to max 10k files) to their searches!
* old OR predicates that you load with saved searches and similar should now always have alphebetised components, and if you double-click them to remove them, they will now clear correctly (previously, they were doing something similar to the recent filetype problem, where instead of recognising themselves and deleting, they would instead duplicate a normalised (sorted) copy of themselves)
* thanks to a user, updated the recently note-and-ai-updated pixiv parser again to grab the canonical pixiv URL and translated tags, if present
* thanks to a user, updated the sankaku parser to grab some more tags
* the file location context and tag context buttons under tag autocompletes now put menu separators between each type of file/tag service in their menus. for basic users, this'll be a separator for every row, but for advanced users with multiple local domains, it will help categorise the list a bit

## [Version 514](https://github.com/hydrusnetwork/hydrus/releases/tag/v514)

### downloaders

* twitter took down the API we were using, breaking all our nice twitter downloaders! argh!
* a user has figured out a basic new downloader that grabs the tweets amongst the first twenty tweets-and-retweets of an account. yes, only the first twenty max, and usually fewer. because this is a big change, the client will ask about it when you update. if you have some complicated situation where you are working on the old default twitter downloaders and don't want them deleted, you can select 'no' on the dialog it throws up, but everyone else wants to say 'yes'. then check your twitter subs: make sure they moved to the new downloader, and you probably want to make them check more frequently too.
* given the rate of changes at twitter, I think we can expect more changes and blocks in future. I don't know whether nitter will be viable alternative, so if the artists you like end up on a nice simple booru _anywhere_, I strongly recommend just moving there. twitter appears to be explicitly moving to non-third-party-friendly
* thanks to a user's work, the 'danbooru - get webm ugoira' parser is fixed!
* thanks to a user's work, the deviant art parser is updated to get the highest res image in more situations!
* thanks to a user's work, the pixiv downloader now gets the artist note, in japanese (and translated, if there is one), and a 'medium:ai generated' tag!

### sidecars

* I wrote some sidecar help here! https://hydrusnetwork.github.io/hydrus/advanced_sidecars.html
* when the client parses files for import, the 'does this look like a sidecar?' test now also checks that the base component of the base filename (e.g. 'Image123' from 'Image123.jpg.txt') actually appears in the list of non-txt/json/xml ext files. a random yo.txt file out of nowhere will now be inspected in case it is secretly a jpeg again, for good or ill
* when you drop some files on the client, the number of files skipped because they looked like sidecars is now stated in the status label
* fixed a typo bug that meant tags imported from sidecars were not being properly cleaned, despite preview appearance otherwise, for instance ':)', which in hydrus needs to be secretly stored as '::)' was being imported as ')'
* as a special case, tags that in hydrus are secretly '::)' will be converted to ':)' on export to sidecar too, the inverse of the above problem. there may be some other tag cleaning quirks to undo here, so let me know what you run into

### related tags overhaul

* the 'related tags' suggestion system, turned on under _options->tag suggestions_, has several changes, including some prototype tech I'd love feedback on
* first off, there are two new search buttons, 'new 1' and 'new 2' ('2' is available on repositories only).. these use an upgraded statistical search and scoring system that a user worked on and sent in. I have butchered his specific namespace searching system to something more general/flexible and easy for me to maintain, but it works better and more comprehensibly than my old method! give it a go and let me know how each button does--the first one will be fast but less useful on the PTR, the second will be slower but generally give richer results (although it cannot do tags with too-high count)
* the new search routine works on multiple files, so 'related tags' now shows on tag dialogs launched from a selection of thumbnails!
* also, all the related search buttons now search any selection of tags you make!!! so if you can't remember that character's name, just click on the series or another character they are often with and hit the search, and you should get a whole bunch appear
* I am going to keep working on this in the future. the new buttons will become the only buttons, I'll try and mitigate the prototype search limitations, add some cancel tech, move to a time-based search length like the current buttons, and I'll add more settings, including for filtering so we aren't looking up related tags for 'page:x' and so on. I'm interested in knowing how you get on with IRL data. are there too many recommendations (is the tolerance too high?)? is the sorting good (is the stuff at the top relevant or often just noise?)?

### misc

* all users can now copy their service keys (which are a technical non-changing hex identifier for your client's services) from the review services window--advanced mode is no longer needed. this may be useful as the client api transitions to service keys
* when a job in the downloader search log generates new jobs (e.g. fetches the next page), the new job(s) are now inserted after the parent. previously, they were appended to the end of the list. this changes how ngugs operate, converting their searches from interleaved to sequential!
* restarting search log jobs now also places the new job after the restarted job
* when you create a new export folder, if you have default metadata export sidecar settings from a previous manual file export, the program now asks if you want those for the new export folder or an empty list. previously, it just assigned the saved default, which could be jarring if it was saved from ages ago
* added a migration guide to the running from source help. also brushed up some language and fixed a bunch of borked title weights in that document
* the max initial and periodic file limits in subscriptions is now 50k when in advanced mode. I can't promise that would be nice though!
* the file history chart no longer says that inbox and delete time tracking are new

### misc fixes

* fixed a cursor type detection test that was stopping the cursor from hiding immediately when you do a media viewer drag in Qt6
* fixed an issue where 'clear deletion record' calls were not deleting from the newer 'all my files' domain. the erroneous extra records will be searched for and scrubbed on update
* fixed the issue where if you had the new 'unnamespaced input gives (any namespace) wildcard results' search option on, you couldn't add any novel tags in WRITE autocomplete contexts like 'manage tags'!!! it could only offer the automatically converted wildcard tags as suggested input, which of course aren't appropriate for a WRITE context. the way I ultimately fixed this was horrible; the whole thing needs more work to deal with clever logic like this better, so let me know if you get any more trouble here
* I think I fixed an infinite hang when trying to add certain siblings in manage tag siblings. I believe this was occuring when the dialog was testing if the new pair would create a loop when the sibling structure already contains a loop. now it throws up a message and breaks the test
* fixed an issue where certain system:filetype predicates would spawn apparent duplicates of themselves instead of removing on double-click. images+audio+video+swf+pdf was one example. it was a 'all the image types' vs 'list of (all the) image types' conversion/comparison/sorting issue

### client api

* **this is later than I expected, but as was planned last year, I am clearing up several obsolete parameters and data structures this week. mostly it is bad service name-identification that seemed simple or flexible to support but just added maintenance debt, induced bad implementation practises, and hindered future expansions. if you have a custom api script, please read on--and if you have not yet moved to the alternatives, do so before updating!**
* **all `...service_name...` parameters are officially obsolete! they will still work via some legacy hacks, so old scripts shouldn't break, but they are no longer documented. please move to the `...service_key...` alternates as soon as reasonably possible (check out `/get_services` if you need to learn about service keys)**
* **`/add_tags/get_tag_services` is removed! use `/get_services` instead!**
* **`hide_service_names_tags`, previously made default true, is removed and its data structures `service_names_to_statuses_to_...` are also gone! move to the new `tags` structure.**
* **`hide_service_keys_tags` is now default true. it will be removed in 4 weeks or so. same deal as with `service_names_to_statuses_to_...`--move to `tags`**
* **`system_inbox` and `system_archive` are removed from `/get_files/search_files`! just use 'system:inbox/archive' in the tags list**
* **the 'set_file_relationships' command from last week has been reworked to have a nicer Object parameter with a new name. please check the updated help!** normally I wouldn't change something so quick, but we are still in early prototype, so I'm ok shifting it (and the old method still works lmao, but I'll clear that code out in a few weeks, so please move over--the Object will be much nicer to expand in future, which I forgot about in v513)
* many Client API commands now support modern file domain objects, meaning you can search a UNION of file services and 'deleted-from' file services. affected commands are
* * /add_files/delete_files
* * /add_files/undelete_files
* * /add_tags/search_tags
* * /get_files/search_files
* * /manage_file_relationships/get_everything
* a new `/get_service` call now lets you ask about an individual service by service name or service key, basically a parameterised /get_services
* the `/manage_pages/get_pages` and `/manage_pages/get_page_info` calls now give the `page_state`, a new enum that says if the page is ready, initialised, searching, or search-cancelled
* to reduce duplicate argument spam, the client api help now specifies the complicated 'these files' and now 'this file domain' arguments into sub-sections, and the commands that use them just point to the subsections. check it out--it makes sense when you look at it.
* `/add_tags/add_tags` now raises 400 if you give an invalid content action (e.g. pending to a local tag service). previously it skipped these rows silently
* added and updated unit tests and help for the above changes
* client api version is now 41

### boring optimisation

* when you are looking at a search log or file log, if entries are added, removed, or moved around, all the log entries that have changed row # now update (previously it just sent a redraw signal for the new rows, not the second-order affected rows that were shuffled up/down. many access routines for these logs are sped up
* file log status checking is completely rewritten. the ways it searches, caches and optimises the 'which is the next item with x status' queues is faster and requires far less maintenance. large import queues have less overhead, so the in and outs of general download work should scale up much better now
* the main data cache that stores rendered images, image tiles, and thumbnails now maintains itself far more efficiently. there was a hellish O(n) overhead when adding or removing an item which has been reduced to constant time. this gonk was being spammed every few minutes during normal memory maintenance, when hundreds of thumbs can be purged at once. clients with tens of thousands of thumbnails in memory will maintain that list far more smoothly
* physical file delete is now more efficient, requiring far fewer hard drive hits to delete a media file. it is also far less aggressive, with a new setting in _options->files and trash_ that sets how long to wait between individual file deletes, default 250ms. before, it was full LFG mode with minor delays every hundred/thousand jobs, and since it takes a write lock, it was lagging out thumbnail load when hitting a lot of work. the daemon here also shuts down faster if caught working during program shut down

### boring code cleanup

* refactored some parsing routines to be more flexible
* added some more dictionary and enum type testing to the client api parameter parsing routines. error messages should be better!
* improved how `/add_tags/add_tags` parsing works. ensuring both access methods check all types and report nicer errors
* cleaned up the `/search_files/file_metadata` call's parsing, moving to the new generalised method and smoothing out some old code flow. it now checks hashes against the last search, too
* cleaned up `/manage_pages/add_files` similarly
* cleaned up how tag services are parsed and their errors reported in the client api
* the client api is better about processing the file identifiers you give it in the same order you gave
* fixed bad 'potentials_search_type'/'search_type' inconsistency in the client api help examples
* obviously a bunch of client api unit test and help cleanup to account for the obsolete stuff and various other changes here
* updated a bunch of the client api unit tests to handle some of the new parsing
* fixed the remaining 'randomly fail due to complex counting logic' potential count unit tests. turns out there were like seven more of them

## [Version 513](https://github.com/hydrusnetwork/hydrus/releases/tag/v513)

### client api

* the Client API now supports the duplicates system! this is early stages, and what I've exposed is ugly and technical, but if you want to try out some external dupe processing, give it a go and let me know what you think! (issue #347)
* a new 'manage file relationships' permission gives your api keys access
* the new GET commands are:
* - `/manage_file_relationships/get_file_relationships`, which fetches potential dupes, dupes, alternates, false positives, and dupe kings
* - `/manage_file_relationships/get_potentials_count`, which can take two file searches, a potential dupes search type, a pixel match type, and max hamming distance, and will give the number of potential pairs in that domain
* - `/manage_file_relationships/get_potential_pairs`, which takes the same params as count and a `max_num_pairs` and gives you a batch of pairs to process, just like the dupe filter
* - `/manage_file_relationships/get_random_potentials`, which takes the same params as count and gives you some hashes just like the 'show some random potential pairs' button
* the new POST commands are:
* - `/manage_file_relationships/set_file_relationships`, which sets potential/dupe/alternate/false positive relationships between file pairs with some optional content merge and file deletes
* - `/manage_file_relationships/set_kings`, which sets duplicate group kings
* more commands will be written in the future for various remove/dissolve actions
* wrote unit tests for all the commands!
* wrote help for all the commands!
* fixed an issue in the '/manage_pages/get_pages' call where the response data structure was saying 'focused' instead of 'selected' for 'page of pages'
* cilent api version is now 40

### boring misc cleanup and refactoring

* cleaned and wrote some more parsing methods for the api to support duplicate search tech and reduce copypasted parsing code
* renamed the client api permission labels a little, just making it all clearer and line up better. also, the 'edit client permissions' dialog now sorts the permissions
* reordered and renamed the dev help headers in the same way
* simple but significant rename-refactoring in file duplicates database module, tearing off the old 'Duplicates' prefixes to every method ha ha
* updated the advanced Windows 'running from source' help to talk more about VC build tools. some old scripts don't seem to work any more in Win 11, but you also don't really need it any more (I moved to a new dev machine this week so had to set everything up again)

## [Version 512](https://github.com/hydrusnetwork/hydrus/releases/tag/v512)

### two searches in duplicates

* the duplicate filter page now lets you search 'one file is in this search, the other is in this search'! the only real limitation is both searches are locked to the same file domain
* the main neat thing is you can now search 'pngs vs jpegs, and must be pixel dupes' super easy. this is the first concrete step towards my plan to introduce an optional duplicate auto resolution system (png/jpeg pixel dupes is easy--the jpeg is 99.9999% always better)
* the database tech to get this working was actually simpler than 'one file matches the search', and in testing it works at _ok_ speed, so we'll see how this goes IRL
* duplicate calculations should be faster in some simple cases, usually when you set a search to system:everything. this extends to the new two-search mode too (e.g. a two-search with one as system:everything is just a one-search, and the system optimises for this), however I also search complicated domains much more precisely now, which may make some duplicate search stuff work real slow. again, let me know!

### sidecars

* the txt importer/exporter sidecars now allow custom 'separators', so if you don't want newlines, you can use ', ' or whatever format you need

### misc

* when you right-click on a selection of thumbs, the 'x files' can now be 'x videos' or 'x pngs' etc.. as you see on the status bar
* when you select or right-click on a selection of thumbs that all have duration, the status bar and menu now show the total duration of your selection. same deal on the status bar if you have no selection on a page of only durating-having media
* thanks to the user who figured out the correct render flag, the new 'thumbnail ui-scale supersampling %' option now draws non-pixelly thumbs on 100% monitors when it is set higher (e.g. 200% thumbs drawing on 100% monitor), so users with unusual multi-monitor setups etc... should have a nicer experience. as the tooltip now says, this setting should now be set to the largest UI scale you have
* I removed the newgrounds downloader from the defaults (this only affects new users). the downloader has been busted for a while, and last time I looked, it was not trivial to figure out, so I am removing myself from the question
* the 'manage where tag siblings and parents apply' dialog now explicitly points users to the 'review current sync' panel

### client api

* a new command, /manage_pages/refresh_page, refreshes the specified page
* the help is updated to talk about this
* client api version is now 39

### server management

* in the 'modify accounts' dialog, if the null account is checked when you try to do an action, it will be unchecked. this should stop the annoying 400 Errors when you accidentally try to set it something
* also, if you do 'add to expires', any accounts that currently do not expire will be deselected before the action too, with a brief dialog note about it

### other duplicates improvements

* I reworked a ton of code here, fixing a heap of logic and general 'that isn't quite what you'd expect' comparison selection issues. ideally, the system will just make more obvious human sense more often, but this tech gets a little complicated as it tries to select comparison kings from larger groups, and we might have some situations where it says '3 pairs', but when you load it in the filter it says 'no pairs found m8', so let me know how it goes!
* first, most importantly, the 'show some random potential pairs' button is vastly improved. it is now much better about limiting the group of presented files to what you specifically have searched, and the 'pixel dupes' and 'search distance' settings are obeyed properly (previously it was fetching too many potentials, not always limiting to the search you set, and choosing candidates from larger groups too liberally)
* while it shows smaller groups now, since they are all culled better, it _should_ select larger groups more often than before
* when you say 'show some random potential pairs' with 'at least one file matches the search', the first file displayed, which is the 'master' that the other file(s) are paired against, now always matches the search. when you are set to the new two-search 'files match different searches', the master will always match the first search, and the others of the pairs will always match the second search. in the filter itself, some similar logic applies, so the files selected for actual comparison should match the search you inputted better.
* setting duplicates with 'custom options' from the thumbnail menu and selecting 'this is better' now correctly sets the focused media as the best. previously it set the first file as the best
* also, in the duplicate merge options, you can now set notes to 'move' from worse to better
* as a side thing, the 'search distance' number control is now disabled if you select 'must be pixel dupes'. duh!

### boring cleanup

* refactored the duplicate comparison statement generation code from ClientMedia to ClientDuplicates
* significantly refactored all the duplicate files calculation pipelines to deal with two file search contexts
* cleaned up a bunch of the 'find potential duplicate pairs in this file domain' master table join code. less hardcoding, more dynamic assembly
* refactored the duplicated 'figure out pixel dupes table join gubbins' code in the file duplicates database module into a single separate method, and rolled in the base initialisation and hamming distance part into it too, clearing out more duplicated code
* split up the 'both files match' search code into separate methods to further clean the logic here
* updated the main object that handles page data to the new serialisable dictionary, combining its hardcoded key/primitive/serialisable storage into one clean dict that looks after itself
* cleaned up the type definitions of the the main database file search and fixed the erroneous empty set returns
* I added a couple unit tests for the new .txt sidecar separator
* fixed a bad sidecar unit test
* 'client_running' and 'server_running' are now in the .gitignore

## [Version 511](https://github.com/hydrusnetwork/hydrus/releases/tag/v511)

### thumbnail UI scaling

* thumbnails can finally look good at high UI scales! a new setting in _options->thumbnails_, 'Thumbnail UI scale supersampling %', lets you tell hydrus to generate thumbnails at a particular UI scale. match it to your monitor, and your thumbnails should regenerate to look crisp
* some users have complicated multi-monitor setups, or they change their UI scale regularly, so I'm not auto-setting this _yet_. let me know how it goes
* sadly <100% for super-crunchy-mode doesn't work

### unnamespaced search tags

* _I am not really happy with this solution, since it doesn't neatly restore the old behaviour, but it does make things easier in the new system and I've fixed a related bug_
* a new option in _services->manage tag display and search_, 'Unnamespaced input gives (any namespace) wildcard results', now lets you quickly search `*:sam*` by typing `sam`
* fixed an issue where an autocomplete input with a total wildcard namespace, like `*:sam` was not matching to unnamespaced tags when preparing the list of tag results
* wildcards with `*` namespace now have a special `(any namespace)` suffix, and they show with unnamespaced namespace colour

### misc

* fixed the client-server communication problem related to last week's SerialisableDictionary update. I messed up and forgot this object is used in network comms, which meant >=v510 clients couldn't talk to a <=509 server and _vice versa_ version swaps. now the server always kicks out an old SerialisableDictionary serialisation. I plan to remove the patch in 26 weeks, giving us more buffer time for users to update naturally
* the recent option to turn off mouse-scroll-changes-menu-button-value is improved--now the wheel event is correctly passed up to the parent panel, so you'll scroll right through one of these buttons, not halt on it. the file sort control now also obeys this option
* if you try to zoom a media in so that its virtual size would be >32,000px on a side, the canvas now zooms to 32k exactly. this is the max allowed zoom for technical reasons atm (I'll fix it in a future rewrite). this also fixes the 'zoom max' command, which previously would make no action if the max zoom created a virtual canvas bigger than this. also, 'zoom max' is now shown on the media viewer right-click menu
* the 'max zoom' dimension for mpv windows and my native animation window is now 8k. seems like there are smaller technical limits for mpv, and my animation window isn't tiled, so this is to be extra safe for now
* fixed a bug where it was possible to send the 'undelete file' signal to a file that was physically deleted (and therefore viewed in a special 'deleted files' domain). the file would obediently return to its original local file service and then throw 'missing file' warnings when the thumb tried to show. now these files are discarded from undelete consideration
* if you are looking at physically deleted files, the thumbnail view now provides a 'clear deletion record' menu action! this is the same command as the button in _services->review services->all local files_, but just on the selection
* fixed several taglists across the program that were displaying tags in the wrong display context and/or not sorting correctly. this mostly went wrong by setting sorted storage taglists (which normally show sibling/parent flare) as unsorted display taglists
* file lookup script tag suggestions (as fetched from some external source) are now set to be sorted

### file import options pre-import checking

* _this stuff is advanced users only. normal users can rest assured that the way the client skips downloads for 'already in db/previously deleted' files now has fewer false negatives and false positives_
* the awkwardly named advanced 'do not check url/hash to see if file already in db/previously deleted' checkboxes in file import options have been overhauled. now they are phrased in the positive ("check x to determine aid/pd?") and offer 'do not check', 'check', and the new 'check - and matches are dispositive'. the tooltip has been updated to talk about what they do. 'dispositive' basically means 'if this one hits, trust it over the other', and by default the 'hash' check remains dispositive over the URLs (this was previously hardcoded, now you can choose urls to rule in some cases).
* there is also a new checkbox to optionally disable a component of the url checking that looks at neighbouring urls on the same file to determine url-mapping trustworthiness. this will solve or help explore some weird multi-url-mapping situations
* also, novel SHA256 hashes no longer count as 'matches', just like a novel MD5 hash would not. this helps keep useful dispositive behaviour for known hashes but also automatically defers to urls when a site is being CDN-optimised and transfer hashes are different to api-reported ones. this fixes some watchers that have been using excess bandwidth on repeated downloads
* fixed several problems with the url-lookup logic, particularly with the method that checks for 'file-neighbour' urls (simply, when a file-url match should be distrusted because that file has multiple urls of the same url class). it was also too aggressive on file/unknown url classes, which can legitimately have tokenised neighbours, and getting confused by http/https dupes
* the neighbour test now remembers untrustworthy domains across different url checks for a file, which helps some subsequent direct-file-url checks where neighbours aren't a marker of file-url mapping reliability
* the overall logic behind the hash and url lookup is cleaned up significantly
* if you are an advanced user who has been working with me on this stuff, let me know how it goes. we erected this rats' nest through years of patches, and now I have cleaned it out. I'm confident it works better overall, but I may have missed one of your complicated situations. at the least, these new options should help us figure out quicker fixes in future

### boring code cleanup

* removed some old 'subject_identifier' arg parsing from various account-modification calls in the server code. as previously planned, for simplicity and security, the only identifier for these actions is now 'subject_account_key', and subject_identifier is only used for account lookups
* improved the error handling around serialised object loading. the messages explain what happened and state object type and the versions involved
* cleaned up some tag sort code
* cleaned up how advanced file delete content updates work
* fixed yet another duplicate potentials count unit test that was sometimes failing due to complex count perspective

## [Version 510](https://github.com/hydrusnetwork/hydrus/releases/tag/v510)

### notes

* duplicate metadata merge options now supports note merging. you can copy from worse to better or in both directions, with a couple extra conflict-resolution options that are a subset of note import options and have reasonable defaults.
* the default note merge options are to go from worse to better for 'set as better' and both directions for 'they are the same', renaming notes on conflicts. **your existing duplicate metadata merge options will receive these settings on update, so if you don't want this, update your settings from the duplicate filter page**
* the manage notes dialog gets copy and paste buttons. these will copy all the current notes and paste them to another instance of the panel, using the default (extend if possible, otherwise rename) conflict resolution rules
* if an automatic system like a parser gives a note text that already exists on the file, the Note Import Options now discards it in all cases, no matter the names involved. no more automatic dupes!
* ADVANCED: note import options (and related note add/merge operations that use it) now scan all prefix-matching note names for 'new note is already in file' and 'new note is an extension of a note already in file' tests. this improves a former fix to the 'successive parses of two sites with the same note name but different note text cause one of them to be dupe-added as (2), (3), (4), renames etc...' bug. the initial (1) rename will be scanned and recognised as 'already in file' and ignored or now extended as the settings say, just as if the desired name were hit. thanks to the reports here--I missed the logic the first time around
* it would be nice to have 'manage notes' for multiple files at once--this is still a future goal

### notes client api

* the `/add_notes/set_notes` now takes some new parameters if you want to apply the adapted Note Import Options merge logic rather than figure out renames and extensions yourself
* `/add_notes/set_notes` now returns the changes it made, which in the new mode may not be exactly what you instructed
* added unit tests and help to reflect the above
* client api version is now 38

### misc

* I fixed up how shift/ctrl/drag selection works on taglists. like with the recent thumbnail selection update, you can now 'undo' a shift-select with subsequent clicks or 'drag undo', and the list remembers what _was_ selected beforehand. ctrl-shift-select is also a more reliable 'deselect range'. both mouse drag selection and ctrl-drag selection use this logic, have fewer index bugs, and the ctrl-drag now chooses at the start whether this drag will be selection or deselection based on your initial click that started the drag. have a play with it--overall it just feels better now
* the 'file log' menu now shows a 'reverse' command, which reverses all the imports in the log. if you want to import from oldest to newest with a typical booru, just start your downloader with file imports paused (check the cog icon), and then allow the gallery search to fully populate the list as normaly. once done, hit this new reverse and then unpause the files, and you should be good
* any image files or thumbnails that are completely transparent and have a non-completely-black image now have their alpha channel stripped, just like files that are completely opaque. I believe the instances where this is a mistake outweigh the instances where it is legit, but let me know how we get on--maybe there are some weird mid-gif thumbs or something where this misfires. in the same thing, I reverted the 'psd thumbnails now have no transparency' change from last week. the issue where ffmpeg was sometimes being confused about psd layer masks from earlier should be fixed while letting legit transparency work correctly. the ultimate fix here will be to roll imagemagick into the program, which I am now planning and will start 'running from source' experiments with soon
* the three 'additional fixed time...' settings in _options->downloading_ now have a max value of 3600, for extreme situation testing

### boring code cleanup

* updated my serialisabledict/list objects again--they can now handle bytes objects in any position. I will slowly migrate my existing hardcoded bytes serialisation and the old serialisablebytesdict to these freshly flexible classes
* for clarity, across the code, renamed 'duplicate action options' to 'duplicate content merge options'
* refactored duplicate content merge options initialisation, clearing the stuffed init and totuple to nicer get/set
* broke apart how NoteImportOptions does its main note filtering for easier low-level access
* cleaned a ton of note import options code up. the logic here was not great, now it is a bit tidier
* undid whatever nonsense I was doing with taglist ctrl-drag-selection and cleaned up the main click and drag event handling along with its index calculation and 'what was clicked last time' record
* fixed numerous weird logical/position index issues with the taglist and clicking/dragging

## [Version 509](https://github.com/hydrusnetwork/hydrus/releases/tag/v509)

### misc

* added an option 'mouse wheel can "scroll" through menu buttons' to _options->gui_. this turns off the behaviour where a mouse wheel event over, for instance, the file sort asc/desc button, will change the button's value rather than scrolling the underlying panel. if you found this annoying, you can finally turn it off!
* fixed an annoying 'save service' bug that some users saw last week with the introduction of serverside Tag Filters. some users had an old datatype in their service data storage--a legacy issue--but the system now coerces all datatypes and direct sub-objects to a saveable format on load or update
* the tag washing system now collapses more types of whitespace character to `space`. mostly this means tab is now converted to space, but some unicode stuff goes too
* the hangul filler character `\u3164` is no longer permitted as a namespace or subtag. it can be in longer tags, but isn't allowed on its own (where it appears to be a blank space). (hydev saw one in the wild, probably from some cheeky post title)
* let me know if you run across a newly invalid tag already in your system and the UI goes bananas--ideally hydrus should now catch this and either fix itself or report with a polite note, but let's see. if things go crazy, run _database->check and repair->fix invaliid tags_
* improved some image transparency detection and slicing logic. it is more accurate and saves more memory now. also, the system that saves thumbnails will more reliably use jpegs when it doesn't need png's transparency
* fixed some PSD thumbs showing a fully transparent transparency layer
* fixed a bug where you could enter capital letters into the namespace colour list in 'tag presentation' options panel
* the default twitter downloaders are all renamed to remove the confusing and technical 'syndication' label
* 'speedcopy' is now an optional supported library. a couple users have suggested this to make network copies on Windows and Linux much faster. I'd like some advanced users who run from source to try adding it to their venvs, and we'll see how it works out IRL in different situations (you can see if it is loaded under _help->about_)
* if you run from source, the 'advanced' setup route now offers a (t)est Qt install, which sets PySide6 6.4.1 (up from 6.3.21). feel free to try it out--it works well for me, but I want to test it more before trying to roll it to the releases
* in a side thing, thanks to the user who walked me through setting up signed commits to github with my own PGP key. you can see my new key in the contacts help page, id 76249F053212133C, and I am now committing with it. I'm not very familiar with the sheer mechanics of this tech, so bear with me, but I'm pretty sure I can sign or encrypt something if ever needed

### macOS build fix

* since v505, many macOS users were unable to boot the built app. it has taken multiple rounds of back and forth with users, but we figured it out. (looks like pyoxidizer updating from 0.22.0 to 0.23.0 simply broke qtpy/Qt bindings, so we force a rollback this week)
* also, the macOS app moves from PySide6 to PyQt6 this week. they are basically the same, but PyQt6 packages into a 258MB dmg, less than half the 548MB PySide6 one!
* let me know if the macOS app gives any more trouble. otherwise, to the people who helped out here, thank you very much for the help!

### mostly boring tag filter panel

* removed the 'add' buttons; added 'delete' buttons to the simple whitelist and blacklist panels; added 'block everything' to simple blacklist panel
* the panel now talks about the special sibling and namespace rules when you edit an explicit blacklist-mode-only filter (the tag import options blacklist works this way)
* the 'you didn't need to add that exception' text and 'filter is too complicated for this panel' texts now show/hide rather than waste empty space
* some of the simple-advanced interactions are better, but there's still some logical bork here. mostly stuff like when you hit the 'unnamespace' checkbox in the whitelist panel, it gets needlessly added to the 'except' column in the advanced, rather than just removed from the advanced 'exclude'. I'll fix this up in the near future
* the two namespace checkbox lists are now sized more appropriately
* the white/blacklist panels disable more simply and reliably

### boring cleanup

* the confusing 'view this file's duplicates' menu label, which was an artifact of an old submenu label, is removed. if the duplicate menu wants to present the 'view' commands for two locations, it'll title with the respective location, otherwise the commands speak for themselves, no label
* some old 'check(er) timings' nomenclature is renamed to 'checker options' across the board
* the hydrus serialisable dictionary now washes any nested lists or dicts to hydrus serialised equivalents, which should stop situations like the save service bug in future
* the hydrus serialisable list can now handle a mix of hydrus serialisables and python primitives. it also washes its lists or dicts to serialisable equivalents
* improved the data-stability of some image channel slicing
* fixed some PIL fallback thumbnail generation, and improved its 'has transparency' png/jpeg decision-making
* fixed the main thumbnail loader being confused at times about which thumbnail mime to load with. the check I have added is ultra-fast on data we are loading anyway, so we shouldn't notice a difference, but if you get slow thumb loads, let me know
* fixed the media container embed buttons using the file mime rather than the thumb mime when loading thumbnails (again causing transparency issues)
* fixed more generally bad mime handling in the thumbnail generation routine that could have caused more unusual transparency handling for clip, psd, or flash files
