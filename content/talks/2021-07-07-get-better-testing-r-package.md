---
title: 'GET better at testing your R package!'
date: '2021-07-07'
event: "useR! 2021"
event_url: "https://user2021.r-project.org/"
event_type: "tutorial"
materials: "https://http-testing-r.netlify.app/"
---

This tutorial is about **Advanced testing of R packages, with HTTP testing as a case study**.
Unit tests have numerous advantages like preventing future breakage of your package and helping you define features (test-driven development).
In many introductions to package development you learn how to set up testthat infrastructure, and how to write a few [“cute little tests”](https://testthat.r-lib.org/articles/test-fixtures.html#test-fixtures) with only inline assertions.
This might work for a bit but soon you will encounter some practical and theoretical challenges: e.g. where do you put data and helpers for your tests? If your package is wrapping a web API, how do you test it independently from any internet connection? And how do you test the behavior of your package in case of API errors?
In this tutorial we shall use HTTP testing with the vcr package as an opportunity to empower you with more knowledge of testing principles (e.g. cleaning after yourself, testing error behavior) and testthat practicalities (e.g. testthat helper files, testthat custom skippers).
After this tutorial, you will be able to use the handy vcr package for your package wrapping a web API or any other web resource, but you will also have gained skills transferable to your other testing endeavours!
Come and learn from rOpenSci expertise!