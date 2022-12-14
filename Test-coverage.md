This article will be more valuable if you are familiar with the Python programming language and Napari software. It is the fourth in a series of articles on testing taken from the [January 2022 testing workshop video](https://drive.google.com/file/d/1DaMrRz-rLRQ6-_y0J8O3GRpVPCn0rgYs/view). The information in this article starts at minute 28:26. This article is a summary of the information in the video and should stand on its own. The other articles are:  
Article 1: [Python’s assert keyword](https://github.com/Nadalyn-CZI/Nadalyn---Test/wiki/Python's-assert-keyword)  
Article 2: [Pytest testing framework](https://github.com/Nadalyn-CZI/Nadalyn---Test/wiki/Pytest-testing-framework)  
Article 3: [Napari Plugins](https://github.com/Nadalyn-CZI/Nadalyn---Test/wiki/Napari-Plugins)  
Article 4: This article  
Article 5: Testing widgets  

This article covers:   
* [Coverage](#Coverage)
* [pytest --cov](#pytest---cov)    
 
#### Resources  
The example plugin and all the tests discussed in this article are available in [this GitHub repository](https://github.com/DragaDoncila/plugin-tests).    

## Coverage  
How do we know when we have tested everything? 

This is where coverage comes in. `pytest-cov` can find out what the coverage of our tests is. Install using 
`pip install pytest-cov`. This feature provides coverage data for pytest. Coverage is the lines of code that were executed when the tests were run. If there are lines of code that didn’t run at all, they represent a code path we didn’t test, which could hold potential bugs. Coverage gives you an idea of other tests that are needed. 

## pytest --cov  

To run tests with coverage, run `pytest` and add `--cov` pointing to the module you're covering. There is also an option to generate an html report, which we do in this case. 

    (napari-env) user@directory % 
    pytest --cov=plugin_tests --cov-report=html .  

Note that the “.” is part of the command.  

    =========================== test session starts ==========================  
    platform darwin -- Python 3.9.7, pytest-6.2.5, py-1.11.0, pluggy-1.0.0  
    PyQt 5.15.6 -- Qt runtime 5.15.2 -- Qt compiled 5.15.2  
    rootdir: qt-4.0.2, napari-plugin-engine-0.2.0, napari-0.4.13, cov-3.0.0  
    collected 2 items  
 
    src/plugin_tests/_tests/test-reader.py ..                           [100%]  
 
    --------- coverage: platform darwin, python 3.0.7-final-0 --------  
    Coverage HTML written to dir htmlcov  
 
    =========================== 2 passed in 6.72s ==========================  

This command runs the tests, gets the coverage, and then writes the Coverage HTML report to the htmlcov directory.

There is a large folder (`htmlcov`) in the directory where the tests were run (`plugin_tests`). 

If we drag the `index.html` file from the list of files in the left panel (to the left of line 32) into a browser, it opens the coverage report as shown below: 

`![htmlcov directory](https://docs.google.com/document/d/1So-4WYSjh5ODi9wmgpu9VgpDXTw09_IcsO02YUnanZM/view)`

We technically have 100% coverage of `test_widget.py` (the fourth line up from Total) because we didn't write a widget. Since there were zero lines of code in the widget, zero were missed, so we had 100% coverage.  

`![Coverage Report](Coverage Report)`

We are interested in `_reader.py`. The file containing the reader code has 86% coverage (see below). Clicking ok on the `2 missing` box below highlights the lines that were never run at all. They are highlighted in red (lines 22 and 26): 

`![Lines not run highlighted in red](Lines not run highlighted in red)`

Because we never provided a list of paths and we never ran code that provided a list of paths, we don't know what will happen in that case. We also never ran code that doesn't return a reader. In other words, we never tested the failed cases. We add those tests. The first one is `test_get_reader_pass`. We'll call it with a file that doesn't end in `.npy` and assert that reader returned `None`. Then we'll create a second test to call it with a list of paths.

Using the `write_im_to_file` fixture again, we can write two files, call `napari_get_reader` with two paths (line 36) and assert that it still returns a callable (line 43).

`![im_to_file fixture](im_to_file fixture)`

If we re-run pytest-cov, the coverage report re-runs. Coverage should improve. All tests are passed. 

The coverage report goes to the same folder, `htmlcov`, so we should be able to refresh the page without dragging it back in. We've got 100% coverage of `_reader.py` now. See below.

`![second coverage report](second coverage report)`  

There could be other, more complicated cases that we have not tested, but at the very least, we are executing all lines of code.

If you are not impressed with the html report, or it's cumbersome, print the coverage to the terminal with `--cov-report=term-missing`. This command is on the [slides](https://docs.google.com/presentation/d/1vD1_jhK6Xjqltmlp5Q2auXkgkvQTrr2d77_a9TqD6yk/edit#slide=id.g10c4a0816be_0_24). That will print the exact lines of code you haven’t tested.

We've got 100% coverage in the reader, no lines missed. In the widget, we missed most (77 of 106) lines. Testing widgets is covered in another article.

`![2 images here](2 images here)`