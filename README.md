# About the project

Check it out: [https://ybogdanov.github.io/history-timeline/](https://ybogdanov.github.io/history-timeline/)

![History Timeline](/public/img/og-image2.jpg?raw=true)

History Timeline is a visualization of the globally famous people lifetimes. It is inspired by Wait But Why's [blog post about Horizontal History](http://waitbutwhy.com/2016/01/horizontal-history.html) — the idea of taking a "horizontal" slice and see history's famous people alive at that time. However, I guess Tim Urban (the Wait But Why's author) had _too much_ fun manually drawing all of these rectangles in the [Numbers](http://www.apple.com/mac/numbers/) spreadsheet. The motivation of this project is to make the idea expandable, interactive, and crowd-sourced, by leveraging from the modern software engineering tools and approaches.

The timeline uses the data set from [Pantheon](http://pantheon.media.mit.edu/), which is a project from the Macro Connections group at The MIT Media Lab. They provide an excellent list of 15,343 historical figures each marked with a [Historical Popularity Index (HPI)](http://pantheon.media.mit.edu/methods) that help us to show the most famous people at the top of the timeline. I scrape the Wikipedia offline dump to get the death dates.

What you see is an open-source MVP that illustrates what's possible with relatively small software engineering effort. It is fun to use as is, but there is a ton of ideas of how the project can be improved further. I will gradually commit to the project, proportionally to the interest of the community. 

I hope to engage other people in contributing and helping to evolve the visualization and data mining aspects of the tool. Here's what you can help with:

1. **Visualizations.** Having the data already provided to the browser, we can improve the way it is displayed.
2. **Data mining**. There is a common data mining pipeline setup, adding new sources was taken into account. The quality of the existing sources scraping can be also improved.
3. **Content**. Besides the programmatic part of data gathering, there's a possibility to tweak the data manually, make corrections, add new historical figures, etc.

There are a lot of glitches right now, such as zero aged people and some obviously popular figures missing on the timeline. The whole point is that you can fix all of it yourself!

If you have any questions/ideas/suggestions, or you have a contribution, feel free to [open an issue](https://github.com/ybogdanov/history-timeline/issues) or [a pull request](https://github.com/ybogdanov/history-timeline/pulls).

# Ideas of improvement

* Plot the most impactful historical events [from here](https://en.wikipedia.org/wiki/Timelines_of_world_history), so you can refer the events to the people lived that time
* Possibly, we can also display some personal events right on the people's rectangles
* Select the particular person so her lifespan is zoomed 90% horizontally, such way we can better inspect the life and the intersection with the other people
* Interactive overlay dialog that shows quick info of the person
* Hovering on a ruler can display a vertical cursor going down that highlights the people alive at that particular time; it may also show the age near every person at a point
* We can show interactively how people relate each other in terms of different generations

Other ideas I've heard from people and not really into, but curious to see the implementations:

* Have an interactive map that can show people who are visible in the viewport
* iPad etc. native implementation
* Displaying avatars over the timeline rectangles

Things that have to be improved:

* Country filter is awful. There are too many countries and coloring them or filtering by the desired country is very inconvenient at the moment. Any ideas?
* The ruler is jumping when you scroll vertically. It's because it is scrolled with Javascript, not with CSS. If you have an idea how to make an element scrollable with the content horizontally, but stay sticky vertically with pure CSS, please suggest.

---

# For contributors

*Note: at the early stage of the project development do not expect to see a fancy stuff like Gulp, Webpack, ReactJS, Babel, Elm, etc. I made it simple and stupid, focusing on the functionality first. The infrastructure may be improved in the future if the project will grow.*

### How it works - data pipeline

First, there is a data mining pipeline — а set of Python scripts that manipulate files (mostly JSON ones) through a multiple steps. Here is a diagram that illustrates the process:

![History Timeline data pipeline](/docs/data-pipeline.png?raw=true)

* **import-pantheon.py** transforms Pantheon data format into the internal one. It also attempts the normalization of names using the large map of redirects extracted from Wikipedia (`redirects_wiki.json`)
* **sort.py** normalizes the list of people that are listed manually in `manual.json`
* **union.py** combines multiple lists, in our case the data from Pantheon and the manual list of people
* **intersect.py** maps the list of people we've got from the sources with the data we scraped from Wikipedia (currently, we map death dates and birth dates if they are missing)
* **final.py** does final sorting by popularity and end normalization, also has optional limiting
* **wrap_jsonp.py** prepares the data to be deliverable to the web browser safely

I will describe the process in more detail once there will be people who are interested to contribute.

### Front-end

The front-end is a simple static html/js/css project. There is no dynamic backend, the site is hosted with [Gihtub Pages](https://pages.github.com/), and you can run it locally using your [favorite static web server](https://gist.github.com/willurd/5720255).

For development, I use this one:

```
ruby -run -ehttpd . -p8000
```

Other notes:

* There is a single [index.html](/index.html) file that renders the main page
* [less](http://lesscss.org/) is used to render css
* [d3](http://d3js.org/) for data visualization
* All javascript logic is in [public/js/main.js](/public/js/main.js)
* The data is loaded using JSONP and is available the in `window.data` variable

### Content contributions

In case you notice that the important figure is missing, or birth/death dates are incorrect, you can edit the [data/sources/manual.json](/data/sources/manual.json) file to fix it and then open a pull request.

### Other stuff

##### git-lfs

The project uses a few data files that are too large to store in Git. I use [git-lfs](https://git-lfs.github.com) for larger files.

If you wanted to work with data and saw something like this, it means you don't have a git-lfs plugin installed:
```
$ make data
bzip2 -dk data/sources/pantheon.json.bz2
bzip2: data/sources/pantheon.json.bz2 is not a bzip2 file.
make: *** [data/sources/pantheon.json] Error 2
```

If you have git-lfs installed, then the data files will be downloaded right when the repository is cloned. Otherwise, there will be a text "pointers" in place of data files. To download them, you have to fetch them with the git-lfs tool. In case you install git-lfs after you cloned the repository, you can use `git lfs pull` command to replace "pointers" with the actual files.

```
$ git lfs ls-files
1cc7f8e11e - data/redirects_wiki.json.bz2
a7003eb432 - data/sources/pantheon.json.bz2
097d0890e6 - data/wiki.json.bz2
```

##### Wikipedia dump

The Wikipedia data is downloaded [from here](http://burnbit.com/torrent/427846/enwiki_20151201_pages_articles_xml_bz2).

The dump is about 52G, so most probably you will want to store it on an external hard drive. You can safely make a symlink of that large file to a project directory, `data/sources/wikipedia.xml` is gitignored.
```
ln -sf YOUR_STORAGE/enwiki-20151201-pages-articles.xml `pwd`/data/sources/wikipedia.xml
```

# Licensed under GPLv2

```
History Timeline is a visualization of the globally famous people lifetimes.
Copyright (C) 2016  Yuriy Bogdanov

This program is free software; you can redistribute it and/or
modify it under the terms of the GNU General Public License
as published by the Free Software Foundation; either version 2
of the License, or (at your option) any later version.

This program is distributed in the hope that it will be useful,
but WITHOUT ANY WARRANTY; without even the implied warranty of
MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE. See the
GNU General Public License for more details.

You should have received a copy of the GNU General Public License
along with this program; if not, write to the Free Software
Foundation, Inc., 51 Franklin Street, Fifth Floor, Boston, MA  02110-1301, USA.
```
