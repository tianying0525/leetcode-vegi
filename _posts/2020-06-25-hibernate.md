---
layout: post
title: Hibernate Tutorial Notes
date: 2020-12-31
Author: Connie 
tags: [tutorial]
comments: false
toc: true
---
### Link
[Hibernate Tutorial | Full Course](https://www.youtube.com/watch?v=JR7-EdxDSf0)

===

#### 3. Hibernate Theory 04:44
**JDBC: Java Database Connectivity**

How Java(variable, object) talks with Database(persistance)

**ORM: Object-relational mapping**

Relational database mapping to object

One of the ways we use to achieve ORM is hibernate. There are other tools like ibatis, toplink...

#### 4. Practical 14:32
[Gradle vs. Maven](https://dzone.com/articles/gradle-vs-maven#:~:text=Gradle%20is%20based%20on%20a,things%20that%20do%20the%20work.%E2%80%9D)

Both of them are build automation systems to consider. Gradle is newer and wins when it comes to "API and implementation dependencites, as well as inherently allowing concurrent safe caches".

```java
public class App{
    public static void main(String[] args){
        ...
        /*Old implementation*/
        Configuration con = new Configuration(); // Until here, we finally find a class, not an interface

        SessionFactory sf = con.buildSessionFactory();

        Session session = sf.openSession();
        
        session.save(newObject);
    }
}
```

#### 5. How to add Hibernate Plugin in Eclipse – 26:27
Nice to have, not applicable in my case.
[Enable Hibernate](https://www.jetbrains.com/help/idea/enabling-hibernate-support.html)

#### 6. Configuration File – 28:07

hibernate.cfg.xml

Driver class

Connection URL(need to specify database name)

```java
Configuration cfg = new Configuration();
cfg.configure("hibernate.cfg.xml").addAnnotatedClass(Alien.class);
// if we are using the default name, we do no need to specify cofig file name here
```

Remember to add @Entity for the class, and add a primary key @Id

Then we sill can't make, why? We need to follow ACID of SQL. We add transaction.

```java
        Configuration con = new Configuration().configure().addAnnotatedClass(Alien.class);

        SessionFactory sf = con.buildSessionFactory();

        Session session = sf.openSession();

        Transaction tx = session.beginTransaction();
        
        session.save(newObject);

        tx.commit();
```

The project is still not working yet, because...next session!

#### 7. Working – 35:20

```xml
<property name = "hbm2ddl.auto">update</property> 
```


Now we update deprecated methods(buildSessionFactory()) for version after maven 4.1

```java
        Configuration con = new Configuration().configure().addAnnotatedClass(Alien.class)

        ServiceRegistry reg = new ServiceRegistryBuilder().applySettings(con.getProperties()).buildServiceRegistry();

        SessionFactory sf = con.buildSessionFactory(reg);

        Session session = sf.openSession();

        Transaction tx = session.beginTransaction();
        
        session.save(newObject);

        tx.commit();
```


#### 8. Show SQL Property – 39:58
```xml
<property name = "show_sql">true</property>
```

#### 9. Annotation – 43:21 
```java
// specify name of table
@Entity
@Table(name = "alien_table")
public class Alien{
    // specify colume name
    @Column(name = "alien_color")
    private String color;
    // not be counted into table
    @Transient
    private String name;
}
```

#### 10. Fetching Data Using Hibernate – 48:08 

```java
...
Transaction tx = session.beginTransaction();

// session.get returns an object of object
newAlien = (Alien) session.get(Alien.class, 101);
```

#### 11. How to use Embeddable Object – 52:37 
 ```java
 @Embeddable
 public class AlienName{
     private String fname;
     private String mname;
     private String lname;
     ....
 }
 ```

 So that, we are inserting these variables into the table, which means we also need not to add @Id here.

 #### 12. Mapping Relations Theory – 01:00:32 
 ##### one-to-one
 intuitive, just add annotation @OneToOne on the object

 ```java
 @Entity
 public class laptop{
     @Id
     private int lid;
     private String lname;
 }

 @Entity
 public class Student{
     @Id
     private int rollno
     private String name
     private int marks
     @OneToOne
     private Laptop laptop;
 }
 ```

##### one-to-many
The below code will generate a new table that has student id and their corresponding laptop lid, which could be redundant.

 ```java
 @Entity
 public class laptop{
     @Id 
     private int lid;
     private String lname;
 }

 @Entity
 public class Student{
     @Id
     private int rollno
     private String name
     private int marks
     @OneToMany
     private List<Laptop> laptops;
 }
 ```

 The updated method might be better, we need our laptop class to know about student as well.

```java
 @Entity
 public class laptop{
     @Id 
     private int lid;
     private String lname;
     @ManyToOne
     private Student stud;
 }

 @Entity
 public class Student{
     @Id
     private int rollno
     private String name
     private int marks
     @OneToMany(mappedBy="std") // mappedBy is essential here, otherwise, same result as above
     private List<Laptop> laptops;
 }
 ```

 ##### many-to-many

 ```java
 @Entity
 public class laptop{
     @Id 
     private int lid;
     private String lname;
     @ManyToMany
     private List<Student> stud;
 }

 @Entity
 public class Student{
     @Id
     private int rollno
     private String name
     private int marks
     @ManyToMany(mappedBy="std") // mappedBy is essential here, otherwise there'll be two more new tables generated each by student and by laptop
     private List<Laptop> laptops;
 }
 ```
#### 13. Mapping Relations Practical – 01:13:36
Remember to add other class to config:

```java
        Configuration con = new Configuration().configure().addAnnotatedClass(Student.class).addAnnotatedClass(Laptop.class)

        ServiceRegistry reg = new ServiceRegistryBuilder().applySettings(con.getProperties()).buildServiceRegistry();

        SessionFactory sf = con.buildSessionFactory(reg);

        Session session = sf.openSession();

        Transaction tx = session.beginTransaction();
        
        session.save(laptop);
        session.save(s);
        
        session.getTransaction().commit;
```
#### 14. Fetch EAGER LAZY – 01:29:

lazy: fetch whenever it's needed

eager: fetch anyways

```java
@Entity
public class Alien{
    ...
    @OneToMany(mappedBy="alien", fetch = FetchType.EAGER) // default is lazy
    private Collection<Laptop> laps = new ArrayList<>();
}
```

#### 15. Hibernate Caching – 01:35:39
**First level cache**: if fetch the same data inside the same session, given by default by hibernate

**Second level cache**: all sessions of the application share this, need to use third party tools such as ehcache, OS...

We need to configure certain things for second level cache:

1. downlaod ehcache, update pem.xml
2. hibernate-ehcache.jar file
3. configure h.c.x file (add certain properties to enable second level cache, and mention your provider)
4. change entity, because by default, not all entities are cachable. @Cachable @Cache

#### 16. Caching Level 1 – 01:44:09
```java
session session1 = sf.openSession();
session1.beginTransaction();

///
a = (Alien) session1.get(Alien.class, 101);
System.out.println(a);

// if primary key(in this case, 101) is the same, only one query will run
b = (Alien) session1.get(Alien.class, 101);
System.out.println(b);
///

session1.getTransaction().commit;

```
However, if another session is running the same query, two queries will run

#### 17. Caching Level 2 – 01:50:39
see video for details

#### 18. Caching Level 2 with Query – 02:00:25
We need to specify one more property in order to use second level cache
```xml
<property name="hibernate.cache.use_query_cache">true</property>
```

also, in query, we need to 
```java
Query q1 = session1.createQuery("from Alien where aid=101");
// here!
q1.setCacheable(true);
...
session1.close();
Query q2 = session2.createQuery("from Alien where aid=101");
// here!
q2.setCacheable(true);
...
session2.close();
```

#### 19. Hibernate Query Language Theory (HQL) – 02:04:53
SQL vs HQL

```java
//SQL
ResultSet rs = st.executeQuery(sql);
while(rs.next()){

}

// HQL
List<Student> st = query.list();
for(Student s : st){
    System.out.println(s);
}
```

#### 20. Hibernate Query Language (HQL) part 1 – 02:09:03

```java
Query  q = session.createQuery("from Student");
// fetch all data
List<Student> students = q.list();

// fetch specific data
Query  q = session.createQuery("from Student where marks > 50");

// fetch a single result
Query  q = session.createQuery("from Student where rollno = 7");
Student student = (Student) q.uniqueResult();

```

#### 21. Hibernate Query Language (HQL) part 2 – 02:18:09
When we would like fetch columns, we need to know that the return type is Object[]
```java
// fetch columns for one unique value
Query  q = session.createQuery("select rollno, name, marks from Student where rollno = 7");
Object[] student = (Object[]) q.uniqueResult();

// fetch coluns for all data
Query  q = session.createQuery("select rollno, name, marks from Student");
List<Object[]> students = (List<Object[]>) q.list();

// fetch more complex things
Query  q = session.createQuery("select sum(marks) from Student s where s.marks>60");
Long marks = (Long) q.uniqueResult();

// "concatenation"
Query  q = session.createQuery("select sum(marks) from Student s where s.marks> :b");
q.setParameter("b", b);
Long marks = (Long) q.uniqueResult();
```

#### 22. Hibernate Query Language (HQL) part 3 – 02:26:58
Working with SQL with Hibernate

```java
// select entire object
SQLQuery query = session.createSQLQuery("select * from student where marks > 60");
// essential if we need student returned, instead of objects
query.addEntity(Student.class);
List<Student> students = query.list();


// select partial
SQLQuery query = session.createSQLQuery("select anme, marks from student where marks > 60");
query.setResultTransformer(Criteria.ALTAS_TO_ENTITY_MAP);
List students = query.list();

for(Object o : students){
    Map m = (Map) o;
    System.out.println(m.get("name") + " : " + m.get("marks"));
}
```

#### 23. Hibernate Object States Persistence Life Cycle – 02:34:03 
There are couple states for an object: transient, presistent, detached, removed
(need to add pics later)

#### 24. Hibernate Object States  (Practical) – 02:40:35

#### 25. Hibernate Get vs Load – 02:47:48 
Difference:

1. **load** gives a proxy object: run query when actually use the object, otherwise not
2. **get** will give null, while **load** give null exception

#### 26. What is JPA? & JPA Implementation – 02:53:17
JPA: Java Persistence API -- standardize presistence methods

After 
1. adding maven dependencies for JPA to pom.xml and 
2. adding persistence.xml file in main/resources/META-INF/persistence.xml 
3. adding annotation on Alien class (@Entity and @Id):

```java
public class App{
    public static void main(String[] args){

        EntityManagerFactory emf = Persistence.createEntityManagerFactory("pu");
        EntityManager em = emf.createEntityManager();
        // get data
        Alien a = em.find(Alien.class, 4);

        // save data
        em.getTransaction().begin();
        em.persist(a);
        em.getTransaction().commit();

    }
}
```

