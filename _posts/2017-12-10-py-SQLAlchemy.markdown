---
layout: post
title: "Py: SQLAlchemy "
subtitle: ""
date: 2017-12-10
author: Sol
category: Py
tags: SQL, db, database
finished: false
mathjax: true
---

## Advanced Database Relationship

### Many to many (with association table) with SQLAlchemy
Consider the classical example of a many-to-many relationship: a database of students
and the classes they are taking. Clearly, you can’t add a foreign key to a class in the
students table, because a student takes many classes—one foreign key is not enough.
Likewise, you cannot add a foreign key to the student in the classes table, because classes
have more than one student. Both sides need a list of foreign keys.

The solution is to add a third table to the database, called an association table. Now the
many-to-many relationship can be decomposed into two one-to-many relationships
from each of the two original tables to the association table. Next picture shows how the
many-to-many relationship between students and classes is represented.

<br>
<img src="/00illustrations/SQLAlchemy/manytomany.png" align="" height="100">
<br>
<br>
The association table in this example is called registrations . Each row in this table
represents an individual registration of a student in a class.
Querying a many-to-many relationship is a two-step process. To obtain the list of classes
a student is taking, you start from the one-to-many relationship between students and
registrations and get the list of registrations for the desired student. Then the one-to-
many relationship between classes and registrations is traversed in the many-to-one
direction to obtain all the classes associated with the registrations retrieved for the stu‐
dent. Likewise, to find all the students in a class, you start from the class and get a list
of registrations, then get the students linked to those registrations.

Traversing two relationships to obtain query results sounds difficult, but for a simple
relationship like the one in the previous example, SQLAlchemy does most of the work.
Following is the code that represents the many-to-many relationship in

```py
registrations = db.Table('registrations',
    db.Column('student_id', db.Integer, db.ForeignKey('students.id')),
    db.Column('class_id', db.Integer, db.ForeignKey('classes.id'))
)

class Student(db.Model):
    id = db.Column(db.Integer, primary_key=True)
    name = db.Column(db.String)
    classes = db.relationship('Class',
                              secondary=registrations,
                              backref=db.backref('students', lazy='dynamic'),
                              lazy='dynamic')

class Class(db.Model):
    id = db.Column(db.Integer, primary_key = True)
    name = db.Column(db.String)
```

Register student for a class:
```py
>>> s.classes.append(c)
>>> db.session.add(s)
```

list the classes student `s` is registered for and list of students reigstered for class `c`:
```py
>>> s.classes.all()
>>> c.students.all()
```