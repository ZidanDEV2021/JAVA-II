Builder
```java
public class User {
  private String firstName;
  private String lastName;
  private int age;

  public User(String firstName, String lastName, int age) {
    this.firstName = firstName;
    this.lastName = lastName;
    this.age = age;
  }

  public static class Builder {
    private String firstName;
    private String lastName;
    private int age;

    public Builder setFirstName(String firstName) {
      this.firstName = firstName;
      return this;
    }

    public Builder setLastName(String lastName) {
      this.lastName = lastName;
      return this;
    }

    public Builder setAge(int age) {
      this.age = age;
      return this;
    }

    public User build() {
      return new User(firstName, lastName, age);
    }
  }
}

User user = new User.Builder()
                .setFirstName("John")
                .setLastName("Doe")
                .setAge(30)
                .build();
```
Hashcode
```java
public class Person {
  private String firstName;
  private String lastName;
  private int age;

  // Constructors, getters, setters

  @Override
  public int hashCode() {
    int result = 17;
    result = 31 * result + firstName.hashCode();
    result = 31 * result + lastName.hashCode();
    result = 31 * result + age;
    return result;
  }
}
```
Log4j
```java
import org.apache.log4j.Logger;

public class MyClass {
  private static final Logger logger = Logger.getLogger(MyClass.class);

  public void doSomething() {
    logger.info("Starting doSomething method");
    // ... do some work
    logger.info("Finished doSomething method");
  }
}
```
...
```java

import java.io.Serializable;
import jakarta.persistence.*;
import lombok.*;

@MappedSuperclass
@EqualsAndHashCode
@ToString
@AllArgsConstructor
@NoArgsConstructor


public class Person implements Serializable {

    @Getter
    @Setter
    @Id
    @GeneratedValue(strategy = GenerationType.AUTO)
    
```
...

```java
@Entity
@AllArgsConstructor
@NoArgsConstructor
@Getter
@Setter
public class Teacher extends Person {

@OneToMany(mappedBy = "teacher", targetEntity = Subject.class)
	private Set<Subject> subjects;
```
...
```java
public class Subject implements Serializable {

@ManyToMany(targetEntity = Student.class,mappedBy = "subjects")
    private Set<Student> students;

    @ManyToOne
    private Teacher teacher;

```
...
```java
public class Student extends Person {
@ManyToMany(targetEntity = Subject.class)
    private Set<Subject> subjects;
```


...

```java
import java.util.*;
import jakarta.persistence.*;
import jakarta.persistence.criteria.CriteriaBuilder;
import jakarta.persistence.criteria.CriteriaQuery;
import jakarta.persistence.criteria.Root;
import lombok.extern.log4j.Log4j2;
import org.junit.jupiter.api.Test;

@Log4j2
public class DemoJPA {

	@Test
	public void demonstrateInheritance() {
		EntityManagerFactory emfactory = Persistence.createEntityManagerFactory(
				"lab10");
		EntityManager entitymanager = emfactory.createEntityManager();
		entitymanager.getTransaction().begin();

		Subject s1 = new Subject();
		s1.setName("Math");

		Subject s2 = new Subject();
		s2.setName("English");
		Set<Subject> subs = new HashSet<>();
		subs.add(s1);
		subs.add(s2);

		// Teaching staff entity
		Teacher ts1 = new Teacher();
		ts1.setSubjects((Set<Subject>) s1);

		Teacher ts2 = new Teacher();
		ts2.setSubjects((Set<Subject>) s2);

		// Non-Teaching Staff entity
		Student nts1 = new Student();
		nts1.setSubjects((Set<Subject>) s1);
		Student nts2 = new Student();
		nts2.setSubjects((Set<Subject>) s2);

		// storing all entities
		entitymanager.persist(ts1);
		entitymanager.persist(ts2);
		entitymanager.persist(nts1);
		entitymanager.persist(nts2);

		entitymanager.getTransaction().commit();
		entitymanager.close();
		emfactory.close();
	}

	@Test
	public void demostrateManyToOne() {
		EntityManagerFactory emfactory = Persistence.createEntityManagerFactory(
				"lab10");
		EntityManager entitymanager = emfactory.createEntityManager();
		entitymanager.getTransaction().begin();

		// Create Department Entity
		Teacher teacher = new Teacher();
		teacher.setFirstName("Janko");
		teacher.setLastName("Mrkvicka");


		entitymanager.persist(teacher);


		Subject subject = new Subject();
		subject.setName("English");
		subject.setTeacher(teacher);
		subject.setCode("ENG");


		Subject subject2 = new Subject();
		subject2.setName("Math");
		subject2.setTeacher(teacher);
		subject2.setCode("MA");


		Subject subject3 = new Subject();
		subject3.setName("Physics");
		subject3.setTeacher(teacher);
		subject3.setCode("PHY");

		// Store Employees
		entitymanager.persist(subject);
		entitymanager.persist(subject2);
		entitymanager.persist(subject3);

		entitymanager.getTransaction().commit();
		entitymanager.close();
		emfactory.close();
	}

	@Test
	public void demostrateOneToMany() {
		EntityManagerFactory emfactory = Persistence.createEntityManagerFactory(
				"lab10");
		EntityManager entitymanager = emfactory.createEntityManager();
		entitymanager.getTransaction().begin();

		Subject subject = new Subject();
		subject.setName("English");

		subject.setCode("ENG");


		Subject subject2 = new Subject();
		subject2.setName("Math");

		subject2.setCode("MA");


		Subject subject3 = new Subject();
		subject3.setName("Physics");

		subject3.setCode("PHY");


		entitymanager.persist(subject);
		entitymanager.persist(subject2);
		entitymanager.persist(subject3);


		List<Subject> sublist = new ArrayList<>();
		sublist.add(subject);
		sublist.add(subject2);
		sublist.add(subject3);

		// Create Department Entity

		Teacher teacher = new Teacher();
		teacher.setFirstName("Janko");
		teacher.setLastName("Mrkvicka");
		teacher.setSubjects((Set<Subject>) sublist);

		// Store Department
		entitymanager.persist(teacher);

		entitymanager.getTransaction().commit();
		entitymanager.close();
		emfactory.close();
	}

	@Test
	public void demostrateOneToManyMapped() {
		EntityManagerFactory emfactory = Persistence.createEntityManagerFactory(
				"lab10");
		EntityManager entitymanager = emfactory.createEntityManager();
		entitymanager.getTransaction().begin();

		Teacher teacher = new Teacher();
		teacher.setFirstName("Janko");
		teacher.setLastName("Mrkvicka");

		entitymanager.persist(teacher);

		Subject subject = new Subject();
		subject.setName("English");
		subject.setTeacher(teacher);
		subject.setCode("ENG");


		Subject subject2 = new Subject();
		subject2.setName("Math");
		subject2.setTeacher(teacher);
		subject2.setCode("MA");


		Subject subject3 = new Subject();
		subject3.setName("Physics");
		subject3.setTeacher(teacher);
		subject3.setCode("PHY");

		entitymanager.persist(subject);
		entitymanager.persist(subject2);
		entitymanager.persist(subject3);

		entitymanager.getTransaction().commit();
		entitymanager.close();
		emfactory.close();

		emfactory = Persistence.createEntityManagerFactory("lab10");
		entitymanager = emfactory.createEntityManager();

		CriteriaBuilder cb = entitymanager.getCriteriaBuilder();
		CriteriaQuery<Teacher> query = entitymanager.getCriteriaBuilder()
				.createQuery(Teacher.class);
		Root<Teacher> root = query.from(Teacher.class);

		for (Teacher res : entitymanager.createQuery(query.select(root).where(cb
						.like(root.get(String.valueOf(Teacher.class)), "Dev%")))
				.getResultList())
		{

			log.info("dep = {}, dep.employee = {}", res.getId(), res.getSubjects());
		}
	}

	@Test
	public void demostrateManyToMany() {

		EntityManagerFactory emfactory = Persistence.createEntityManagerFactory(
				"lab10");
		EntityManager entitymanager = emfactory.createEntityManager();
		entitymanager.getTransaction().begin();

		Subject subject = new Subject();
		subject.setName("English");
		subject.setCode("ENG");


		Subject subject2 = new Subject();
		subject2.setName("Math");
		subject2.setCode("MA");

		Subject subject3 = new Subject();
		subject3.setName("Physics");
		subject3.setCode("PHY");

		entitymanager.persist(subject);
		entitymanager.persist(subject2);
		entitymanager.persist(subject3);

		Set<Subject> subSet1 = new HashSet<>();
		subSet1.add(subject);
		subSet1.add(subject2);
		subSet1.add(subject3);

		Set<Subject> subSet2 = new HashSet<>();
		subSet2.add(subject3);
		subSet2.add(subject);
		subSet2.add(subject2);

		Set<Subject> subSet3 = new HashSet<>();
		subSet3.add(subject2);
		subSet3.add(subject3);
		subSet3.add(subject);

		Student stud1 = new Student();
		stud1.setFirstName("Janko");
		stud1.setLastName("Mrkvicka");
		stud1.setSubjects((Set<Subject>) subject);
		Student stud2 = new Student();
		stud2.setFirstName("Karel");
		stud2.setLastName("Mrkvicka");
		stud2.setSubjects((Set<Subject>) subject2);
		Student stud3= new Student();
		stud3.setFirstName("Marek");
		stud3.setLastName("Mrkvicka");
		stud3.setSubjects((Set<Subject>) subject3);



		// Store Teacher
		entitymanager.persist(stud1);
		entitymanager.persist(stud2);
		entitymanager.persist(stud3);

		entitymanager.getTransaction().commit();
		entitymanager.close();
		emfactory.close();

	}
	@Test
	public void demonstrateCriterie() {

		EntityManagerFactory emfactory = Persistence.createEntityManagerFactory(
				"lab10");
		EntityManager entitymanager = emfactory.createEntityManager();
		entitymanager.getTransaction().begin();

		Subject subject = new Subject();
		subject.setName("English");
		subject.setCode("ENG");


		Subject subject2 = new Subject();
		subject2.setName("Math");
		subject2.setCode("MA");


		Subject subject3 = new Subject();
		subject3.setName("Physics");
		subject3.setCode("PHY");

		// Store Employee
		entitymanager.persist(subject);
		entitymanager.persist(subject2);
		entitymanager.persist(subject3);
		entitymanager.getTransaction().commit();

		entitymanager.close();
		emfactory.close();

	}

```


```
