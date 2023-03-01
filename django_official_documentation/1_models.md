# Models

A model is an information about your data. It contains fields and behaviors of the data. Each model is a single database table.

Every Django model subclasses `django.db.models.Model` which will be covered later. Each attribute of the model is a table column.

## Model Fields

All models can have a few arguments. The following arguments are available to all field types. All are optional:

__null__: If `True`, Django will store empty values as null in the database. Default is `False`. Avoid using null for string-based fields and use blank instead. Never use blank and null for a string-based field at the same time.

__blank__: If `True`, the field is allowed to be blank. Default is `False`. Keep in mind that null is database-related, but blank is validation related.

__choices__: Choices limits the input from the user to the particular values. Choices must be a list consisting of tuples. First element of each tuple is the actual value and second element is the human-readable value. Example:

```python
from django.db import models


class Student(models.Model):
    FRESHMAN = 'FR'
    SOPHOMORE = 'SO'
    JUNIOR = 'JR'
    SENIOR = 'SR'
    GRADUATE = 'GR'
    YEAR_IN_SCHOOL_CHOICES = [
        (FRESHMAN, 'Freshman'),
        (SOPHOMORE, 'Sophomore'),
        (JUNIOR, 'Junior'),
        (SENIOR, 'Senior'),
        (GRADUATE, 'Graduate'),
    ]
    year_in_school = models.CharField(
        max_length=2,
        choices=YEAR_IN_SCHOOL_CHOICES,
        default=FRESHMAN,
    )

    def is_upperclass(self):
        return self.year_in_school in {self.JUNIOR, self.SENIOR}
```

Note: A new migration is created each time the order of choices changes.

__db_column__: Use this option if you want to change field's column name in database table.

__help_text__: Use this option if you want to give information about the value you are expecting for this field.

__validators__: You can create validators for your fields. Example:

```python
from django.db import models
from django.core.exceptions import ValidationError


def validate_name(value):
    if value == 'admin':
        raise ValidationError("Your name cannot be admin.")


class Student(models.Model):
    name = models.CharField(max_length=255, validators=[validate_name])
```

There are other useful options too, but they will not be covered in this article. Please read [this page](https://docs.djangoproject.com/en/4.1/ref/models/fields/#field-options) if you are interested.

## Relationships between models

### Many-to-one

To define a many-to-one relationship, use `ForigenKey` field type:

```python
from django.db import models


class Manufacturer(models.Model):
    pass


class Car(models.Model):
    manufacturer = models.ForeignKey(Manufacturer, on_delete=models.CASCADE)
```

### Many-to-many

To define a many-to-many relationship, use `ManyToManyField` field type, but keep mind this field should be only in one model not both:

```python
from django.db import models


class Topping(models.Model):
    pass


class Pizza(models.Model):
    toppings = models.ManyToManyField(Topping)
```

## Meta Class

Model Meta is basically the inner class of your model class. Model Meta is basically used to change the behavior of your model fields like changing order options, `verbose_name`, and a lot of other options. It's completely optional to add a Meta class to your model. Let's explore a few of Meta class options:

__db_table__: This is used to change table name of the model.

```python
from django.db import models


class Ox(models.Model):
    horn_length = models.IntegerField()

    class Meta:
        db_table = 'ox_table'
```

__ordering__: This is used to tell Django how to order the queries.

```python
class MyModel(models.Model):
    # Fields
    class Meta:
        ordering = ['-pub_date', 'author']
```

This is to order by `pub_date` descending, then by author ascending.

There are a lot of other Meta class options which are not included here. Visit [this link](https://docs.djangoproject.com/en/4.1/ref/models/options/) to get more information.
