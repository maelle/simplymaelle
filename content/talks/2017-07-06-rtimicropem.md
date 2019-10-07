---
title: 'Using an R package as platform for harmonized cleaning of data from RTI MicroPEM air quality sensors'
date: '2017-07-06'
event: "useR!"
event_url: "https://user2017.brussels/"
event_type: "lightning talk"
slides: "https://static.sched.com/hosted_files/user2017/47/ecology_statistics_550.pdf"
video: "https://channel9.msdn.com/Events/useR-international-R-User-conferences/useR-International-R-User-2017-Conference/Room-201-Lightning-Talks"
links: 
  - name: package
    url: "https://docs.ropensci.org/rtimicropem/"
---

RTI MicroPEM is a small particulate matter personal exposure monitor, increasingly used in developed and developing countries. Each measurement session produces a csv file which includes a header with information on instrument settings and a table of thousands of observations of time-varying variables such as particulate matter concentration, relative humidity. Files need to be processed for 1) generating a format suitable for further analysis and 2) cleaning the data to deal with the instruments shortcomings. Currently, this is not done in a harmonized and transparent way. Our package pre-processes the data and
converts them into a format that allows the integration the rich set of data manipulation and visualization functionalities that the **tidyverse** provides.

We made our software open-source for better reproducibility, easier involvement of new contributors and free use, particularly in developing countries. We applied the package in a research project for a large number of measurements. The functionalities of our package are three-fold: allowing conversion of files, empowering easy data quality checks, and supporting reproducible data cleaning through documentation of current workflows.

For inspection of individual files, the package has a R6 class where each object represents one MicroPEM file, with summary and plot methods including interactivity thanks to **rbokeh**. The package also contains a **Shiny** app for exploration by non-experienced *R* users. The **Shiny** app includes a tab with tuneable alarms, e.g. "Nephelometer slope was not 3" which empowered rapid checks after a day on the field. For later stages of a study after a bunch of files has been collected, the package supports the creation of a measurements and a settings *data.frames* from all files in a directory. We exemplify data cleaning processes, in particular the framework used for the CHAI project, in a vignette, in a transparency effort.

The package is currently available on Github. Since air pollution sensors that would output csvy (csv file with yaml frontmatter) instead of weird csv; and produce ready-to-use data are currently unavailable, **rtimicropem** can be an example of how to use an *R* package as a central place for best practices, thus fostering reproducibility and harmonization of data cleaning across studies. We also hope it can trigger more use of *R* in the fields of epidemiology and exposure science.
