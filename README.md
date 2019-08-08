# NetworkX Tutorial

This tutorial was written by <a href="https://www.grinnell.edu/users/waldenka">Katherine Walden</a>, Digital Liberal Arts Specialist at Grinnell College.

This tutorial was reviewed by <a href="https://www.grinnell.edu/users/purcelsj">Sarah Purcell</a> (L.F. Parker Professor of History) and <a href="https://www.grinnell.edu/users/donovang">Gina Donovan</a> (Instructional Technologist) at Grinnell College, and edited by Papa Ampim-Darko, a student research assistant at Grinnell College.

This tutorial is adapted from the Programming Historian’s <a href="https://programminghistorian.org/en/lessons/exploring-and-analyzing-network-data-with-python">Exploring and Analyzing Network Data with Python tutorial</a>.

<a href="http://creativecommons.org/licenses/by-nc/4.0/" rel="license"><img style="border-width: 0;" src="https://i.creativecommons.org/l/by-nc/4.0/88x31.png" alt="Creative Commons License" /></a>
Network Analysis-Part II (NetworkX) is licensed under a <a href="http://creativecommons.org/licenses/by-nc/4.0/" rel="license">Creative Commons Attribution-NonCommercial 4.0 International License</a>.

<hr />

<em>GUI interfaces like Palladio and Gephi help researchers use digital tools quickly without requiring extensive technical documentation. But, larger data sets or more complex analysis often goes beyond the capacity of a GUI tool. In the next section of the tutorial, we will be using <a href="https://networkx.github.io/documentation/stable/index.html">NetworkX</a>, a Python package, to analyze and study more complex aspects of the Quaker data. NetworkX is a free Python library developed and released in 2005.</em>

<hr />

## Getting the Data

We will be working with sample networked data sets based on the <a href="http://www.oxforddnb.com/">Oxford Dictionary of National Biography</a> and the <a href="http://www.sixdegreesoffrancisbacon.com/">Six Degrees of Francis Bacon</a> project. These data sets include a list of names and relationships for early seventeenth-century Quakers.

Download the the quakers_nodelist and quakers_edgelist CSV files from this repository 

Save the files to your Desktop.

Open these files in Microsoft Excel to explore the data structure.

## Installing NetworkX

<p align="center"><a href="https://github.com/kwaldenphd/NetworkX-tutorial/blob/master/screenshots/capture_networkx.PNG?raw=true"><img class="aligncenter size-full wp-image-602" src="https://github.com/kwaldenphd/NetworkX-tutorial/blob/master/screenshots/capture_networkx.PNG?raw=true" alt="" width="740" height="736" /></a></p>

In a working Python environment, navigate to ..\Python27\Scripts in File Explorer.

<p align="center"><a href="https://github.com/kwaldenphd/NetworkX-tutorial/blob/master/screenshots/Capture_3.png?raw=true"><img class="aligncenter size-full wp-image-600" src="https://github.com/kwaldenphd/NetworkX-tutorial/blob/master/screenshots/Capture_3.png?raw=true" alt="" width="982" height="514" /></a></p>

Type cmd in the File Explorer navigation bar, and hit the Enter key. A command prompt window will open.

Type `pip install networkx` in the command prompt window and hit the Enter key to install the NetworkX Python library.

<p align="center"><a href="https://github.com/kwaldenphd/NetworkX-tutorial/blob/master/screenshots/Capture_4.png?raw=true"><img class="aligncenter size-full wp-image-601" src="https://github.com/kwaldenphd/NetworkX-tutorial/blob/master/screenshots/Capture_4.png?raw=true" alt="" width="979" height="281" /></a></p>

Type `pip install community` in the command prompt window and hit the Enter key to install the Community Python library.

Close the command prompt window after the install has completed.

If you want to install Python or NetworkX on your own computer, consult <a href="https://programminghistorian.org/en/lessons/exploring-and-analyzing-network-data-with-python">The Programming Historian’s tutorial</a> for additional instructions.

<hr />

## Loading Network Data in Python

1-We will be working with the same nodelist and edgelist CSV files we downloaded at the start of this tutorial. <strong>Copy or move those files into your Python directory (C:Python…) using File Explorer.</strong>

2-Click <strong>Start-&gt;All Programs-&gt;Python-&gt;IDLE (Python)</strong> to open Python’s GUI coding interface.

3-Start your Python file by importing the libraries and packages we’ll need to use NetworkX.

```python

import csv
import networkx as nx
from operator import itemgetter
import community

```

4-CSV and itemgetter are built-in Python libraries, and we installed the NetworkX and Community libraries in the first part of this tutorial.

5-Now we’ll add code that tells Python to open the nodelist and edgelist CSV files and read the data contained in those files. Feel free to re-type this code, but you can also copy and paste from the online WordPress tutorial.

```python 
with open('quakers_nodelist.csv', 'r') as nodecsv: # Open the file                   
    nodereader = csv.reader(nodecsv) # Read the csv
    nodes = [n for n in nodereader][1:] # Retrieve the data (using Python list comprehension and list slicing to remove the header row)
node_names = [n[0] for n in nodes] # Get a list of only the node names
with open('quakers_edgelist.csv', 'r') as edgecsv: # Open the file
    edgereader = csv.reader(edgecsv) # Read the csv
    edges = [tuple(e) for e in edgereader][1:] # Retrieve the data
```

6-`With open` opens our CSV files in the read (‘r’) mode and creates lists named nodecsv and edgecsv with that data. The additional indented lines read the CSV data, analyze the node names, retrieve the edge data, and remove the descriptive header information.

7-From these commands, we have a list of node names and edge pairs which will be the foundation of our network analysis.

8-Before moving forward, let’s check to make sure our data has loaded correctly into Python.

```python
print(len(node_names)) # prints the count of node names
print(len(edges)) # prints the total count of edges
```

9-If you reopened the CSV files, you could confirm that Python had correctly loaded 119 node names (number of rows in the nodelist file) and 174 edges (number of rows in the edgelist file).

<hr />

## Creating a NetworkX Graph

10-Right now, we have a list of <strong>nodes</strong> (node_names) and a list of <strong>edges</strong> (edges) in Python. NetworkX will allow us to combine those lists in a network analysis, a <strong>Graph data object</strong> in NetworkX.

11-Use G=nx.Graph() to initialize a Graph object. Right now the G graph object is empty.
<ul>
 	<li>G.add_nodes_from(node_names) adds node names from our node_names list.</li>
 	<li>G.add_edges_from(edges) adds edges from our edges list</li>
</ul>

```python
G=nx.Graph() 
G.add_nodes_from(node_names) 
G.add_edges_from(edges)
```

12-Use `print(nx.info(G))` to output information about our newly-created Graph object, including the number of nodes, edges, and the average degree (average number of connections for each node). Keep in mind, unlike the network graph we created in Palladio, NetworkX is creating a Graph data object, not a network visualization.

```python
print(nx.info(G))
```
<hr />

## Adding Data Attributes in NetworkX

13-The power of NetworkX allows you to add attributes to the nodes and edges in your Graph object. We’ll add this data through a two-step process—creating a Python dictionary for the attribute values, then connecting the attributes in the dictionary to the appropriate node.

<blockquote><em>Hang in there—the next step is the most technically complex part of this tutorial.</em></blockquote>

14-We’ll create empty dictionaries for each of the attribute fields in our nodelist CSV file.

```python 
hist_sig_dict = {}
gender_dict = {}
birth_dict = {}
death_dict = {}
id_dict = {}
```

15-We’ll now tell Python to go through the data in the nodes list (from the nodelist CSV) and add the appropriate items to each dictionary. Python is able to do this because our data from the CSV files is structured and has position or index information that enables Python to correctly put items in dictionaries.

```python
for node in nodes: # Loop through the list, one row at a time    
    hist_sig_dict[node[0]] = node[1]    
    gender_dict[node[0]] = node[2]    
    birth_dict[node[0]] = node[3]    
    death_dict[node[0]] = node[4]    
    id_dict[node[0]] = node[5]
 ```
 
16-`For node in nodes` and the subsequent indented lines created a set of Python dictionaries we can now use to add those attributes to our Graph data object in NetworkX.

17-NetworkX uses a set_nodes_attributes function to connect the Graph object and the dictionary with attribute information, and creates a name for the newly-added attribute information

```python 

nx.set_node_attributes(G, hist_sig_dict, 'historical_significance') 
nx.set_node_attributes(G, gender_dict, 'gender')
nx.set_node_attributes(G, birth_dict, 'birth_year')
nx.set_node_attributes(G, death_dict, 'death_year')
nx.set_node_attributes(G, id_dict, 'sdfb_id')

```

18-G is our Graph object, hist_sig_dict (etc) is our dictionary with attribute data, and ‘historical_significance’ is the name for the new attribute data in the Graph object.

19-Now that all of our nodes are connected to attribute data, we can print out specific combinations of nodes and attributes.

```python

for n in G.nodes(): # Loop through every node, in our data "n" will be the name of the person    
    print(n, G.node[n]['birth_year']) # Access every node by name and "birth_year" attribute
```
<hr />

## Calculating metrics in NetworkX

Given how easily we were able to load data and visualize a network in Palladio, the detailed Python coding may seem like an unnecessary burden. However, the metrics NetworkX allows us to calculate for our network data provide greater insight and detail that the visualizations we were able to generate in Palladio.

<hr />

### Structural Calculations

#### 20-Density

```python 

density=nx.density(G)
print(“Network density:”, density)

```

Creates an object named density and uses NetworkX to calculate density for our Graph object. `Print` displays the output for the density calculation.

On a scale of 0 to 1, our network is not very dense, which is consistent with the visualizations we saw in Palladio.

#### 21-Shortest path

```python

fell_whitehead_path=nx.shortest_path(G, source=”Margaret Fell”, target=”George Whitehead”)
print(“Shortest path between Fell and Whitehead:”, fell_whitehead_path)

```

Creates an object named fell_whitehead_path and uses NetworkX to calculate the shortest path (or number of connections) between Margaret Fell and George Whitehead.

Why would a shortest path calculation be useful? In this case, we learn the shortest path from Fell to Whitehead goes through George Fox, a connection that might not have been clear in a network visualization.

#### 22-Diameter

```python 

diameter=nx.diameter(G)

```
Running the `diameter=nx.diameter(G)` command will give us the error message that diameter cannot be calculated since our Graph is not connected.

A connected Graph would be one in which all nodes are connected in some way. If we remember our Palladio visualization, not all of our nodes are connected.

Instead, we can identify the most significant component or section of our network and calculate that component’s diameter.

```python

# If your Graph has more than one component, this will return False

print(nx.is_connected(G)) 

# Next, use nx.connected_components to get the list of components, then use the max() command to find the largest one:

components = nx.connected_components(G)
largest_component = max(components, key=len) 

# Create a "subgraph" of just the largest component, then calculate the diameter of the subgraph, just like you did with density.

subgraph = G.subgraph(largest_component)
diameter = nx.diameter(subgraph)
print("Network diameter of largest component:", diameter)
```

<hr />

### Centrality Measures

#### 23-Degree

```python 

degree_dict=dict(G.degree(G.nodes()))
nx.set_node_attributes(G, degree_dict, ‘degree’)
print(degree_dict)
```

Creates an object named degree_dict and uses NetworkX to calculate the degree of centrality on the nodes in the Graph object. The second line of code adds that centrality calculation to each node as an attribute.

But a useful way to use the degree calculation is to compare relative centrality of different nodes, which we can do using the sort and itemgetter functions.

```python

sorted_degree=sorted(degree_dict.items(), key=itemgetter(1), reverse=True)
print(sorted_degree)
```

Don’t worry too much about all  that’s going on with this code—you are identifying the dictionary you want to sort (degree_dict), selecting the items from within that dictionary that you want to sort (key=itemgetter(1)), and reversing the sorted function so the highest degree of centrality nodes will appear first on the list.

#### 24-Betweenness centrality

However, we probably didn’t need a degree of centrality calculation to confirm William Penn and George Fox would be significantly central figures in our network of 17<sup>th</sup> century Quakers.

Betweenness centrality calculates the number of shortest path routes that go through a specific node. These ‘broker’ nodes often may not be central in the standard network calculation, but they are incredibly significant to the network’s structure.

```python 

betweenness_dict = nx.betweenness_centrality(G) # Run betweenness centrality
nx.set_node_attributes(G, betweenness_dict, 'betweenness')
sorted_betweenness = sorted(betweenness_dict.items(), key=itemgetter(1), reverse=True)
 
print("Top 20 nodes by betweenness centrality:")
for b in sorted_betweenness[:20]:
    print(b)
 ```
    
As we see in these results, nodes with significant Betweenness Centrality calculations are not the same list that appeared for the standard Centrality Degree calculations. Women like Mary Penington and Elizabeth Leavens have increased significance as broker nodes, when their role was not visible in the standard centrality calculations.

We are only scratching the surface of the metrics you can calculate on a network using NetworkX. For more information and examples, consult <a href="https://programminghistorian.org/en/lessons/exploring-and-analyzing-network-data-with-python">The Programming Historian’s tutorial</a>.

<hr />

### Exporting data from NetworkX

25-NetworkX allows us to calculate a wide range of metrics within Python, but we might want to export the network data so we can visualize it outside the Python environment.

```python
nx.write_gexf(G, ‘quaker_network.gexf’)
```

This command exports the NetworkX Graph data object as a Gephi file, which can be opened in the Gephi network visualization software.

If you navigate to your <strong>C:Python… directory</strong> in <strong>File Explorer</strong>, you will see a <strong>quaker_network GEXF</strong> Graph File.

<hr />

# Reflection questions
<ul>
 	<li>This was a more technically-oriented tutorial than most others in the course. What did you enjoy? What did you find challenging?</li>
 	<li>What was the most confusing or unclear part of the tutorial? What questions do you have about network analysis and the digital tools historians can use to analyze networks?</li>
 	<li>What was the most surprising or interesting part of the tutorial?</li>
 	<li>How did you understanding of the data set change over the course of the tutorial—what thoughts or questions did you have about the data when we were working with Palladio, versus NetworkX, versus Gephi?</li>
 	<li>What types of historical data might be well-suited for network analysis? What kinds of data might not work well for network analysis?</li>
 	<li>How would you move forward with a network analysis project? What types of data would you be interested in exploring, and what kinds of research questions would you want to ask?</li>
</ul>
