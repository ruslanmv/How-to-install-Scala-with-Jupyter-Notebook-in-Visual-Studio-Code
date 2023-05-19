# How to run Scala in Visual Studio Code with Jupyter Notebook and Spark 


### Step 1 - Installation of Spark and Scala 
Let us assume you have already installed propertly Scala and Spak on your computer,  you can follow this [tutorial](https://ruslanmv.com/blog/How-to-install-Spark-on-Windows).

Moreover we assume you have installed Python from [here](https://www.python.org/downloads/windows/)  and
Visual Studio from [here](https://go.microsoft.com/fwlink/?LinkID=534107)  you can follow this [tutorial](https://ruslanmv.com/blog/How-to-install-Jupyter-Notebook-in-Visual-Studio-Code). 

### Step 2 - Creation of Project
Create your project folder:

For example,
We open a terminal
```
cd \
```
amd we create folder called myproject
```
mkdir myproject
```
we enter to the folder
```
cd  myproject
```

then we open VS code


```
code .
```
then we open a new terminal there and we choose the Command Prompt 

### Step 3- Installation of the Enviroment

In the Command Prompt we create a virtual environment by typing
```
python -m venv .myenv

```
that would create a virtual environment named ‘.myenv’ and
then we activate the enviroment

```
.myenv\Scripts\activate.bat

```


then we install the notebook


```
pip install notebook
```
and the Scala Kernel


```
pip install spylon-kernel
```

![](assets/20230519151854.png)

then we create a kernel spec


```
python -m spylon_kernel install 
```

![](assets/20230519152407.png)



### Step 5. Update jupyter.kernels.trusted


Open the VS User Settings UI from the Command Palette (Ctrl+Shift+P) and find `Preferences: Open User Settings` 

![](assets/20230519172440.png)

Search for the setting `jupyter.kernels.trusted`
![](assets/20230519172848.png)

Copy the fully qualified path to the kernelspec,

```
C:\ProgramData\jupyter\kernels\spylon-kernel\kernel.json
```

**Add Item** into the list using the Add button

and we have
![](assets/20230519173107.png)
Re-load VS Code, by closing and opening. You can return back to you directory `C:\myproject>` and type

```
code .
```


## Step 4 Create a Jupyter Notebook for Scala
We create a Jupyter Notebook by running the 
`Create: New Jupyter Notebook` command from the Command Palette (Ctrl+Shift+P)
![](assets/20230519153647.png)


### Step 4 Select a kernel
After a new notebook was created  click select **Another kernel** 

![](assets/20230519164253.png)

then pick **Jupyter Kernel**

![](assets/20230519164350.png)

Next, select a kernel **spylon_kernel** using the kernel picker in the top right.

![](assets/20230519153933.png)


After selecting a kernel, the language picker located in the bottom right of each code cell will automatically update to the language supported by the kernel.  you can see your enviroment installed here. We have choosen
spylon_kernel  that uses **Scala** language
![](assets/20230519154141.png)



## Step 5 - Hello World in Scala
Let us test our Scala enviroment in Jupyter Notebook
Let us copy the following cell in the notebook.




```
println("Hello World from Jupyter Notebook in VS Code")

```
and you will get

![](assets/20230519173622.png)

additionally you can see your Spark Job 


![](assets/20230519173722.png)

### Checking Spark Version
Like any other tools or language, you can use –version option with spark-submit, spark-shell, and spark-sql to find the version.

```
!spark-submit --version
!spark-shell --version
!spark-sql --version
```

or simply type

```
sc.version
```

or 

```
spark.version
```
you will have something like this
##
![](assets/20230519175808.png)

#### Reading files from Scala Notebook
One of the first importat things during the creation of any software are the Environment variables.
Environment variables allow us to define values for given keys on the OS, which applications can read. 

```scala
val SPARK_HOME=sys.env.get("SPARK_HOME").mkString 
val FILE = "\\examples\\src\\main\\resources\\people.json"
val PATH=SPARK_HOME+FILE
val df = spark.read.json(PATH)
df.show()
```
![](assets/20230519182806.png)

### Other languages

Since this makes use of metakernel you can evaluate normal python code using the `%%python` magic. In addition once the spark context has been created the `spark` variable will be added to your python environment.

#### Reading files in Python from Scala Notebook

The scala spark metakernel provides a scala kernel by default. On the first execution of scala code, a spark session will be constructed so that a user can interact with the interpreter.



```python
%%python
import os
SPARK_HOME=os.environ['SPARK_HOME']
FILE = "examples/src/main/resources/people.json"
PATH=os.path.join(SPARK_HOME, FILE)
df = spark.read.json(PATH)
df.show()
```
![](assets/20230519174645.png)


## Step 6 Create a Jupyter Notebook for Python with Scala Support

If we want to work in Python with Scala support we can get the Scala code inside the Python Code as a magic.

We create a  new Jupyter Notebook by running the 
`Create: New Jupyter Notebook` command from the Command Palette (Ctrl+Shift+P)
![](assets/20230519153647.png)


After a new notebook was created  click select **Another kernel** 

![](assets/20230519164253.png)

then pick **Jupyter Kernel**

![](assets/20230519164350.png)

Next, select a kernel **myproject** using the kernel picker in the top right.

![](assets/20230519183930.png)


After selecting a kernel,   you can see your enviroment. We have choosen
spylon_kernel  that uses **Python** language

![](assets/20230519184132.png)

### Hello World in Python with Scala support from Jupyter Notebook


Spylon-kernel can be used as a magic in an existing ipykernel. 
This is the recommended solution when you want to write
relatively small blocks of scala.


If you just want to send a string of scala code to the interpreter and evaluate it you can
do that too.

```python
from spylon_kernel import get_scala_interpreter

interp = get_scala_interpreter()

# Evaluate the result of a scala code block.
out=interp.interpret("""
val string = "Hello World in Python with Scala support"
string
""")
```

```
print(out)
```
![](assets/20230519200454.png)

### Load Jars

The launch arguments can be customized using the `%%init_spark` magic as follows

```python
%%init_spark
launcher.jars = ["file:///C:\myproject\Hello-World-in-Scala-Notebook.ipynb"]
launcher.master = "local[4]"
launcher.conf.spark.executor.cores = 8
```
