# ElectronTests

[![Build Status](https://travis-ci.com/beacon-biosignals/ElectronTests.jl.svg?branch=master)](https://travis-ci.com/beacon-biosignals/ElectronTests.jl)
[![Codecov](https://codecov.io/gh/beacon-biosignals/ElectronTests.jl/branch/master/graph/badge.svg)](https://codecov.io/gh/beacon-biosignals/ElectronTests.jl)

Build for and with JSServe!

Usage:
Allows you to run javascript inside your app to test all sorts of values:

```julia
using ElectronTests
using JSServe: @js_str, Slider, Button, TextField, linkjs, onjs
using JSServe.DOM
using ElectronTests: testsession, runjs, @wait_for, trigger_keyboard_press
using Test
using Markdown

function test_handler(session, req)
    s1 = Slider(1:100)
    s2 = Slider(1:100)
    b = Button("hi")
    t = TextField("Write!")

    linkjs(session, s1.value, s2.value)


    dom = md"""
    # IS THIS REAL?

    My first slider: $(s1)

    My second slider: $(s2)

    Test: $(s1.value)

    The BUTTON: $(b)

    Type something for the list: $(t)

    some list $(t.value)
    """
    return DOM.div(dom, id="testapp")
end

testsession(test_handler) do app
    @test runjs(app, js"document.getElementById('testapp').children.length") == 1
    @test runjs(app, js"document.getElementById('testapp').children[0].children[0].innerText") == "IS THIS REAL?"
    @test runjs(app, js"document.querySelectorAll('input[type=\"button\"]').length") == 1
    @test runjs(app, js"document.querySelectorAll('input[type=\"range\"]').length") == 2
end
```
