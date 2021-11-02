# python-dataclasses

### What is a Python dataclass?
Python dataclasses are very similar to normal classes, but with implemented class functionalities that significantly decrease the amount of boilerplate code required to write

### Using the normal Python class
When working with a normal class in Python, we have longer code to implement the base methods.
* "__repr__" returns the string representation of a class instance. This prints the attributes of each class instance in a string form.
* "_lt_" compares the population of two Country instances and returns True if the present instance has a lesser population
* "_eq_" returns True if they both have the same population count

```python

    class Country:
        def __init__(self, name:str, population: int, continent: str, official_lang: str = "English"):
            self.name = name
            self.population = population
            self.continent = continent
            self.official_lang = official_lang
        
        def __repr__(self):
            return (f"Country(name={self.name}), population=({self.population}), continent=({self.continent}), official_lang=({self.official_lang})")

        def __lt__(self, other):
            return self.population < other.population

        def __eq__(self, other):
            return self.population == other.population

    
    africa_country = Country("Gambia", 2521126, "Africa", "English")
    small_africa = Country("Maldives", 552595, "Asia", "Dhivehi")
    print(africa_country)
    # Country(name=Gambia), population=(2521126), continent=(Africa), official_lang=(English)
    print(africa_country < small_africa) # False

```

### Using the Python dataclass
To use Python’s dataclass in your code, simply import the module and register the @dataclass decorator on top of the class. This injects the base class functionalities into our class automatically.

In the following example, we’ll create the same Country class, but with far less code:

```python

from dataclasses import dataclass

@dataclass(order=True)
class Country:
    name: str
    population: int
    continent: str
    official_lang: str

africa_country = Country("Gambia", 2521126, "Africa", "English")
print(africa_country)

```

### Manipulating object fields using the field() function
The "dataclass" module also provides a function called "field()". This function gives you ingrained control over the class fields, allowing you to manipulate and customize them as you wish.

For example, we can exclude the "continent" field when calling the representation method by passing it a "repr" parameter and setting the value to "false":
```python

from dataclasses import dataclass, field

@dataclass
class Country:
    name: str
    population: int
    continent: str = field(repr=False) # omits the field
    official_lang: str

smallestEurope = Country("Monaco", 37623, "Europe", "French")

print(smallestEurope)

# Country(name='Monaco', population=37623, official_lang='French') 

```

### init parameter
The "init" parameter passes to specify whether an attribute should be included as an argument to the constructor during initialization. If you set a field to "innit=False, then you must omit the attribute during initialization. Otherwise, a "TypeError" will be thrown


### repr parameter
The "repr" parameter passes to specify if the field should be included "(repr=True)" or excluded "(repr=False)" from the string representation, as generated by the "__repr__" method:

```python

from dataclasses import dataclass, field

@dataclass
class Country:
    name: str
    population: int  
    continent: str
    official_lang: str = field(repr=False) # This field will be excluded from string representation


smallestEurope = Country("Monaco", 37623, "Europe", "French") 

print(smallestEurope)

```

### Modifying fields after initialization with __post_init__
The __post_init__ method is called just after initialization. In other words, it is called after the object receives values for its fields, such as name, continent, population, and official_lang.

For example, we will use the method to determine if we are going to migrate to a country or not, based on the country’s official language:

```python

from dataclasses import dataclass, field

@dataclass
class Country:
    name: str
    population: int
    continent: str = field(repr=False) # Excludes the continent field from string representation
    will_migrate: bool = field(init=False) # Initialize without will_migrate attribute
    official_lang: str = field(default="English") # Sets default language. Attributes with default values must appear last


    def __post_init__(self):
        if self.official_lang == "English":
            self.will_migrate == True
        else:
            self.will_migrate == False 

```
After the object initializes with values, we perform a check to see if the official_lang field is set to English from inside post_init. If so, we must set the will_migrate property to true. Otherwise, we set it to false.

### Sort and compare dataclasses with sort_index
Another functionality of dataclasses is the ability to create a custom order for comparing objects and sorting lists of objects.

For example, we can compare two countries by their population numbers. In other words, we want to say that one country is greater than another country if, and only if, its population count is greater than the other:

```python

from dataclasses import dataclass, field

@dataclass(order=True)
class Country:
    sort_index: int = field(init=False)
    name: str
    population: int = field(repr=True)
    continent: str 
    official_lang: str = field(default="English") #Sets default value for official language



    def __post_init__(self):
        self.sort_index = self.population

smallestEurope = Country("Monaco", 37623, "Europe")
smallestAsia= Country("Maldives", 552595, "Asia")
smallestAfrica= Country("Gambia", 2521126, "Africa") 

print(smallestAsia < smallestAfrica) # True
print(smallestAsia > smallestAfrica) # False

```