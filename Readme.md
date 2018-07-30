# How to use my code to visualize QuarkerChain
In this tutorial , I will give all the instructions on how to build up Spyder (python enviorment) and necessary packages, then you can easily use my code to visualize all token transactions.

## 1.Set up python
* install [python](https://www.python.org), follow the instructions on official website, should be easy.
* install Anaconda(optional but strongly recommend),download latest installer .exe from [here](https://anaconda.org), click 'Next' all the way.
* After installation, type **'Spyder'** in your windows search menu, open the Spyder software, there you can run all the python code.

## 2.Install packages
* type **'Anaconda Prompt'** in windows search menu, then install all packages needed:
```
 powershell
 cd /path/to/repo
 pip install -r Requirements.txt
```
## 3.Run your code
* simply run **'scrape.py'** to obtain all the transaction data, save as '.csv' file. I leave the token_address and total_page for your own choice
* run **'return_neighbors.py'** to obtain the sub-graph of the interested address.(you can input level you want , and the address you want to query)
* after running, open quarker.html to view and interact with your graph and clusters.
