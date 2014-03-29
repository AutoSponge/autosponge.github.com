---
published: true
layout: post
category: blog
tags: [javascript, api, testing, functional]
---

[CucumberJS](https://github.com/cucumber/cucumber-js) helps JavaScript programmers by providing a test
harness for the analyst-friendly, [BDD](http://cukes.info/) language, Gherkin.  One of the most
recent additions to the project, _Scenario Outlines and Examples_, gives you even more power to
cover derivatives of the "happy path."

{% highlight cucumber %}
Feature: GET requests return available resources
  As an unauthenticated user
  I can request resources

Background:
  Given I call the API
    | environment | baseURL                           |
    | DEV         | https://raw.githubusercontent.com |

  Scenario Outline:  'search' rules message
    When I send a GET request to <path>
    Then the response code is <code>
    And the response body is <body>

  Examples:
    | path                                        | code | body                            |
    | /AutoSponge/api-cucumber/master/test_data/1 | 200  | {"id": 1,"message": "success"}  |
    | /AutoSponge/api-cucumber/master/test_data/2 | 404  | Not Found                       |
{% endhighlight %}

All of the CucumberJS examples I found only test browser interactions through Zombie.  But, since I
test APIs as much as browser features, I figured someone else might find this useful as they start
their own project.

I made the [api-cucumber](https://github.com/AutoSponge/api-cucumber) repo to act as a reference.  Per usual,
I try to write the code in a readable, functional style.  As always, I welcome feedback.
