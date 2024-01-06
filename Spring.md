# Spring

## These are the steps we need to follow to create a spring project.

- Spring initializer and downloading it
- Importing the downloaded package to eclipse.
- Create a database in mysql workbench
- Go to resources/application.properties and add the configurations of the database to the project as below.

spring.datasource.url=jdbc:mysql://localhost:3306/postcode_api
spring.datasource.username=root
spring.datasource.password=mypass
spring.jpa.hibernate.ddl-auto=update

- Go to src/main/java, right click on the main package and create a sub-package. The sub-package should include the main packages name. com.behzad.employeecreatorbackend.greeting
- In the sub-package create a controller. GreetingController.
- Tell spring that this is a controller. (@RestController annotation)
- Tell spring that a http request will be coming to a certain endpoint (@RequestMapping1("/greetings"))

- To tell spring that there is a get request we use @GetMapping
  http://localhost:8080/greetings will get us hello world.

- To use url in the returned string we can do:
  @GetMapping("/{name}")
  public String helloWithName(@PathVariable String name) {
  return String.format("Hello %s", name);
  }

## Creating an Entity

- Again create a sub-package. Here we will put everything we want to store in the db for each of the records.
- We declare the fields(title, content, category, createdAt, etc.)
- We need to have a constructor to create an entity.
- Spring requires to have the default constructor anyway. So we add it.
- To tell spring that this is an entity we add @Entity
- We need to add a primary key as a unique identifier and use @Id.
  To tell the database to generate an id automatically we use @GeneratedValue(strategy=GenerationType.IDENTITY)
  We don't need to have the id in the constructor because the database will do that for us.
- To change the table name we use @Table.
- All the field should have @Column annotation.

## Creating a Controller

- Add @RestController before the class
- Add @RequestMapping before the class
- @GetMapping for the get method
- We need to return a response entity

```java
public ResponseEntity<List<Post>> getAll(){

}
```

- We need to forward the data to the Service Layer. So, We need to create the service layer at this point.

## Creating a Service Layer

- To tell Spring that this is a service we add @Service
- We also add @Transactional. It will wrap every process of doing something to the database in a single transaction.
- In the service layer if we are trying to get something we just have to request it from the db. For this we need to use the repository layer. Here we create a Repository Layer (Which will be an interface!)

```java
public List<Post> getAll() {

	}
```

## Creating a Repository Layer

- The repository interface should extend JpaRepository
  public interface PostRepository extends JpaRepository<EntityNameGoesHere, DataTypeThatWasUsedForId> {
  }

## Back to service layer

- Give the service access to the repository layer (use the repository as one of the properties);

```java
 @Autowired
  private PostRepository postRepository;

	public List<Post> getAll() {
		return this.postRepository.findAll();
	}
	//this.postRepository= new PostRepository(); We don't need to do this because spring does it for us through dependency injection @Autowired.
```

- Instead of creating an instance of PostRepository we let Spring handle this situation using Autowired. So we don't end up having multiple instances of post repository in case we need to have this in another class. This is called **Dependency Injection**.
- In the service layer we need to return this.postRepository.findAll()
- Here in the PostService we get some data from the db and we want to send that data as part of our response in the controller.

## Back to controller layer

- Here in the Controller the same way we needed the repository Autowired for our service we need the service Autowired for the controller.
- We need to change the returned data type in the controller from List to ResponseEntity which enables us to customize the status code.

```java
@Autowired
private PostService postService;

 @GetMapping
  public ResponseEntity<List<Post>> getAll(){
  List<Post> allPosts= this.postService.getAll();
  return new ResponseEntity<>(allPosts, HttpStatus.OK);
  }

```

# Creating a post method

- In the controller we start with @PostMapping
- Again we need to return a ResponseEntity but only a single post not a list of posts.
- Here we need to create a DTO. (PostCreateDTO)

## PostCreateDTO

```java
public class PostCreateDTO {

	@Getter
	@Setter
	private String title;

	@Getter
	@Setter
	private String content;

	@Getter
	@Setter
	private String category;

	public PostCreateDTO(String title, String content, String category) {
		this.title=title;
		this.content=content;
		this.category=category;

	}

}
```

## PostController

- To tell the spring that the data is coming from the request body we need to add @RequestBody to the argument of create method.
- To see that the data is actually coming from the request body we can write this for now.

```java
public ResponseEntity<Post> createPost(@RequestBody PostCreateDTO data){
	System.out.println(data.getCategory()); //These will print in the terminal and we don't get anything in the postman.
	System.out.println(data.getContent());
	System.out.println(data.getTitle());
	return null
}
```

- To forward the data to the service layer:

```java
public ResponseEntity<Post> createPost(@RequestBody PostCreateDTO data){
	Post newPost= this.postService.createPost(data)// We need to crete this method in the PostService
}
```

## PostService

- In the service layer we extract the fields from DTO, and add the extra ones like createAt. We use this to create an instance of the entity. We also clean up the data if necessary (like trim(), ...). We use the repository layer to save the post and also return it.

```java
public class PostService {

	@Autowired
	private PostRepository postRepository;

	public List<Post> getAll() {
		return this.postRepository.findAll();
	}

	public Post createPost(PostCreateDTO data) {
		String title = data.getTitle().trim();
		String category = data.getCategory().trim();
		String content = data.getContent().trim();
		Date createdAt = new Date();
		Post newPost = new Post(title, content, category, createdAt);

		Post created= this.postRepository.save(newPost); // the save method saves the record and also returns the record from the database with an id.
		return created;
	}

}
```

## PostController

- Now in the controller we can just return a new ResponseEntity

```java
	@PostMapping
	public ResponseEntity<Post> createPost(@Valid @RequestBody PostCreateDTO data) {
		Post newPost = this.postService.createPost(data);
		return new ResponseEntity<Post>(newPost, HttpStatus.CREATED);
	}
```

## PostCreateDTO

- In the DTO we can add @NotBlank to the fields to make sure that they are present in the request body. You cannot use @NotBlank for Integer (not int- do some research).

- In order for the DTO validations to work we need to add @Valid in the controller

# Creating a getById method

## PostController

```java
@GetMapping("/{id}")
	public ResponseEntity<Post> getById(@PathVariable Long id){
		Post found= this.postService.getById(id);
	}
//Here we need to go to PostService and create getById() method
```

## PostService

```java

public Optional<Post> getById(Long id) {
		// we use postRepository.findById(id); // this repository method returns an optional. It doesn't throw an error if it doesn't exist.

   Optional<Post> foundPost= postRepository.findById(id);
	 //We can decide what to do here.
	 // Return the optional and handle it in the controller.
	 // or we could throw an error here (We would need to try and catch in the controller)
	 return foundPost;
	 //We will throw an error in the controller instead of here.

	}
```

## PostController

```java

	@GetMapping("/{id}")
	public ResponseEntity<Post> getById(@PathVariable Long id){
		Optional<Post> found= postService.getById(id);
		if(found.isPresent()) {
			return new ResponseEntity<Post>(found.get(),HttpStatus.OK );
		}

		//get() is a method on Optional values that returns their value.

```

- Here we can configure something called a global exception handler. So we can use it wherever we want to throw an exception. (in deleteById, updateById)

## Creating a new package called Exceptions

- In this package we start by creating NotFoundException class.

## NotFoundException

- This class needs to extend RuntimeException.
- It will require us to add

```java
	private static final long serialVersionUID = 1L;
```

- Our exception needs to have a message.
- Also we need to have a constructor and a getter for the message.
- Because it extends the RuntimeException we need to call the parent constructor using super();

## GlobalExceptionHandler

- Now we will create a class called GlobalExceptionHandler.
- Here we need to add @ControllerAdvice annotation. This way we are letting the spring know this is something the spring should be using if a certain exception gets thrown.
- Then we need a method that returns a ResponseEntity because that's what we want to show to the user.
- The body of ResponseEntity won't be a Post because it does not exist. It is only a String with an error message.
- This method also needs an annotation of what exception it will be triggered on. @ExceptionHandler(NotFoundException.class)

```java

@ControllerAdvice
public class GlobalExceptionHandler {

	@ExceptionHandler(NotFoundException.class)
	public ResponseEntity<String> handleNotFoundException(NotFoundException ex) {
		return new ResponseEntity<>(ex.getMessage(), HttpStatus.NOT_FOUND);
	}
}
```

- Now that we have this set up we can go to PostController and use it.

## PostController

```java

@GetMapping("/{id}")
	public ResponseEntity<Post> getById(@PathVariable Long id){
		Optional<Post> found= postService.getById(id);
		if(found.isPresent()) {
			return new ResponseEntity<Post>(found.get(),HttpStatus.OK );
		}
		throw new NotFoundException(String.format("Post with id: %d does not exist", id));

	}

```

# Deleting a Post

## PostController

- We need to add the annotation @DeleteMapping("/{id}") in the controller.

```java
@DeleteMapping("/{id}")
	public ResponseEntity<Suburb> deleteById(@PathVariable Long id){
		this.suburbService.deleteById(id);
	}
```

- We need to create deleteById(id) in the PostService

## PostService

- Here we can choose to return a boolean in the service to return true if delete was successful and false if it wasn't.

```java

	public boolean deleteById(Long id) {
		Optional<Suburb> foundPost = this.getById(id);
		if (foundPost.isPresent()) {
			this.postRepository.delete(foundPost.get());
			return true;
		}
		return false;
	}

```

- Now we go back to controller and use this.

## PostController

```java
	@DeleteMapping("/{id}")
	public ResponseEntity<Post> deleteById(@PathVariable Long id) {
		boolean deleted = this.postService.deleteById(id);
		if (deleted == true) {
			return new ResponseEntity<>(null, HttpStatus.NO_CONTENT);
		}

		throw new NotFoundException(String.format("Post with id: %d does not exist, could not delete.", id));
	}
```

# Updating a Post

## PostController

- The annotation would be @PatchMapping("/{id}").
- We would also need @PathVariable @RequestBody.

## PostUpdateDTO

- We need to create PostUpdateDTO.
- We might not need a constructor for this class.
- We cannot add @NotBlank to the fields because they might not be in the request body.
- Instead of NotBlank we can add the annotation @Pattern(regexp = "^(?=\\S).\*$", message = "Title cannot be empty")

```java
public class PostUpdateDTO {

	@Getter
	@Setter
	@Pattern(regexp = "^(?=\\S).*$", message = "Title cannot be empty")
	private String title;

	@Getter
	@Setter
	@Pattern(regexp = "^(?=\\S).*$", message = "Content cannot be empty")
	private String content;

	@Getter
	@Setter
	@Pattern(regexp = "^(?=\\S).*$", message = "Category cannot be empty")
	private String category;

}
```

## PostController

```java

	@PatchMapping("/{id}")
	public ResponseEntity<Post> updateById(@PathVariable Long id, @RequestBody PostUpdateDTO data) {
		this.postService.updateById(id, data);
	}

```

- Now we need to set up the service method.

## PostService

- Here instead of doing this:

```java

public Optional<Post> updateById(Long id, PostUpdateDTO data) {
		Optional<Post> foundPost = this.getById(id);

		if (foundPost.isPresent()) {
			Post toUpdate = foundPost.get();
			if (data.getTitle() != null) {
				toUpdate.setTitle(data.getTitle());
			}
			if (data.getContent() != null) {
				toUpdate.setContent(data.getContent());
			}
			if (data.getCategory() != null) {
				toUpdate.setCategory(data.getCategory());
			}

		}

		return foundPost;
	}

```

- We can use model mapper. A model mapper takes in an object an turns it into a different object.
- After setting up the model mapper we can also use it in the createPost

## ModelMapper

- We can use https://modelmapper.org/
- Add the dependency in the pom.xml
- Pause the app, right click on the project, go to Maven and click update project. Click ok.
- Create a new package and call it config.
- Add a class and call it ModelMapperConfig.
- Add the annotation of @Configuration.
- In the body of the class we need the annotation of @Bean
- Beans are objects that are managed by spring. Spring will create them and make them available to all of our classes that need them.

```java

@Configuration
public class ModelMapperConfig {

	@Bean
	public ModelMapper modelMapper() {
		ModelMapper mapper = new ModelMapper();
		mapper.getConfiguration().setSkipNullEnabled(true);
		// What that means is when the user sends a request that is missing some
		// properties, they won't overwrite the previous values. It skips updating them.

		return mapper;

	}

}
```

## StringTrimConverter

- If we wan't to make a change to a data type we need to create a converter. For example for trimming the strings.
- To create a converter we need to create a new package and call it converters.
- Then we create a class and call it StringTrimConverter.
- We need to implement Converter<S,D> in this class. S is source data type and D is destination data type. <String,String>
- import org.modelmapper.Converter;
- Now it wants us to implement the method convert(String source)
- Here we need to change the parameter because what we want to take in as a parameter is a MappingContext.
- import org.modelmapper.spi.MappingContext;

```java

package com.zad.postcodeapi.converters;

import org.modelmapper.Converter;
import org.modelmapper.spi.MappingContext;

public class StringTrimConverter implements Converter<String, String> {

	@Override
	public String convert(MappingContext<String, String> context) {
		if (context.getSource() == null) {
			return null;
		}
		return context.getSource().trim();

	}

}

```

## ModelMapper

- Now we need to use the converter in the model mapper.

```java

@Configuration
public class ModelMapperConfig {

	@Bean
	public ModelMapper modelMapper() {
		ModelMapper mapper = new ModelMapper();

		mapper.typeMap(String.class, String.class).setConverter(new StringTrimConverter());
		mapper.getConfiguration().setSkipNullEnabled(true);


		return mapper;

	}

}

```

## PostService

- Now we can use the model mapper in the service layer.
- To do this we use dependency injection again. (@Autowired)

```java

	@Autowired
	private ModelMapper modelMapper;

```

- We turn this into an optional because we decided that return data type is optional.

```java
	public Optional<Post> updateById(Long id, PostUpdateDTO data) {
		Optional<Post> foundPost = this.getById(id);

		if (foundPost.isPresent()) {
			Post toUpdate = foundPost.get();

			modelMapper.map(data, toUpdate);
			Post updatedPost = this.postRepository.save(toUpdate);

			// We turn this to an optional because the data type that we are returning is an
			// optional. This is an optional that always exists!
			return Optional.of(updatedPost);

		}

		return foundPost;
	}
```

## PostController

```java

@PatchMapping("/{id}")
	public ResponseEntity<Post> updateById(@PathVariable Long id, @RequestBody PostUpdateDTO data) {
		Optional<Post> updated = this.postService.updateById(id, data);

		if (updated.isPresent()) {
			return new ResponseEntity<Post>(updated.get(), HttpStatus.OK);
		}

		throw new NotFoundException(String.format("Post with id: %d does not exist, could not update", id));

	}
```

- We can use the model mapper for createPost method in the service layer.

## PostService

- Instead of having :

```java

public Suburb createSuburb(SuburbCreateDTO data) {

		String title = data.getTitle();
		int content = data.getContent();
		String category = data.getCategory();

		Post newPost = new Post(title, content, category);
		Post created = this.postRepository.save(newPost);
		return created;
	}

```

## WebConfig

- In order to be able to access our spring app from web browser we need to do this configurations.
- We need @Configuration annotation.
- This should implement WebMvcConfigurer interface.
- import org.springframework.web.servlet.config.annotation.WebMvcConfigurer;
- In the class body we need to implement addCorsMappings method. This way we can whitelist a list of origins and allow requests from those origins.

```java
String[] allowedOrigins= {"http://localhost:5173/","http://127.0.0.1:5173/" };
```

- And then we need to tell the app which paths are allowed to make requests. And also allowed methods, and allowed headers.

```java
import org.springframework.context.annotation.Configuration;
import org.springframework.web.servlet.config.annotation.CorsRegistry;
import org.springframework.web.servlet.config.annotation.WebMvcConfigurer;

@Configuration
public class WebConfig implements WebMvcConfigurer {

	public void addCorsMappings(CorsRegistry registry) {

		String[] allowedOrigins = { "http://localhost:5173/", "http://127.0.0.1:5173/" };

		registry.addMapping("/**").allowedOrigins(allowedOrigins).allowedMethods("GET", "POST", "DELETE", "PATCH")
				.allowedHeaders("*");
	}

}

```

# Writing Tests for Spring Apps

- We will be testing the repository layer and the service layer.
- These are unit tests so we are isolating each layer that is being tested.
- To test the service layer we need to be going to mock the behavior of the repository layer.
- We don't need to test the standard methods that spring provided for us.
- If we write a method in the Repository layer like findByTitle(String title) we also don't need to test it because spring will generate the code for you.
- The only time that you would need to test the repository is when you do something like below to have some custom methods.

```java

import org.springframework.data.jpa.repository.JpaRepository;
import org.springframework.data.jpa.repository.Query;

public interface PostRepository extends JpaRepository<Post, Long> {

	@Query("" +
            "SELECT CASE WHEN COUNT(s) > 0 THEN " +
            "TRUE ELSE FALSE END " +
            "FROM Post s " +
            "WHERE s.title = ?1"
						//Give me true if a post exist, give me false if it doesn't.
    )
	Boolean selectExistsTitle(String title);

//	Post findByTitle(String title);

}

```

- This is java persistence query language (JPQL) which looks like SQL.
- Because the repository layer directly interacts with the database and we don't want it to interact with the actual database.
- We want a fresh database without any data for the purpose of testing only.
- So the same way we have our resources folder in the main and we have application.properties, we will create one in the test folder.
- Right-click on src/test and create a folder called resources.
- Then in the folder we create a file and call it application.properties.
- Because we need the database only for testing we will create an in-memory database instead of sql database. This is an h2 database available in spring that has some pre-existing username and password that we can use for testing.
- To do this we need to add two dependencies to our pom.xml.

```java

	<dependency>
			<groupId>com.h2database</groupId>
			<artifactId>h2</artifactId>
			<scope>test</scope>
		</dependency>
		<dependency>
			<groupId>org.postgresql</groupId>
			<artifactId>postgresql</artifactId>
			<scope>runtime</scope>
		</dependency>

```

## application.properties

- We need to add the below configuration to the new application.properties.

```conf
spring.datasource.url=jdbc:h2://mem:db;DB_CLOSE_DELAY=-1
spring.datasource.username=sa
spring.datasource.password=sa
spring.datasource.driver-class-name=org.h2.Driver
#The following line will create the tables for us every time
# we run the test and then it will drop those tables at the end.
spring.jpa.hibernate.ddl-auto=create-drop
#The following line is used to control whether to output SQL statements to the console
spring.jpa.show-sql=true
spring.jpa.properties.hibernate.dialect=org.hibernate.dialect.PostgreSQLDialect
spring.jpa.properties.hibernate.format_sql=true
```

## PostRepositoryTest

- Navigate to src/test/java.
- Right click on the main package and create a new class and call it PostRepository.java
- For each test we need @Test annotation because for the assertions we use JUnit 5.
- For the class we need @DataJpaTest
- import org.springframework.boot.test.autoconfigure.orm.jpa.DataJpaTest;
- We can also use @AutoConfigureTestDatabase(replace= Replace.NONE) if we want to use the same database for the tests that we are using for the main application.
- We need to Autowire the repository.
- Here we want to test the method selectExistsTitle(String title).
- We want to see if there is a post with that title this method should return true.
- The database is empty so we need to create a record.
- After creating the record we need to save it.
- To run the test right-click and run as JUnit test.

```java

import static org.junit.jupiter.api.Assertions.assertFalse;
import static org.junit.jupiter.api.Assertions.assertTrue;

import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.autoconfigure.orm.jpa.DataJpaTest;

import com.zad.postcodeapi.suburb.Suburb;
import com.zad.postcodeapi.suburb.SuburbRepository;

@DataJpaTest
public class SuburbRepositoryTest {

	@Autowired
	private SuburbRepository underTest;

	@Test
	void selectExistsTitile_TitleExists_ReturnTrue() {

		String title = "How to test Spring apps?";
		Post post = new Post(title, "some content", "some category");
		this.underTest.save(post);
		Boolean exists = this.underTest.selectExistsTitle(title);
		assertTrue(exists);

	}

	@Test
	void selectExistsTitle_TitleDoesntExist_ReturnsFALSE() {
		Boolean exists = this.underTest.selectExistsTitle("Some new title");
		assertFalse(exists);

	}

}

```

## PostServiceTest

- This is not a DataJPA test so we don't need that annotation.
- We need to mock the dependencies of this class so the test happens in isolation.
- We need the @Mock annotation for any dependency in the service layer. Because for example we don't wanna interact with the actual repository. import org.mockito.Mock;
- We also need @ExtendWith(MockitoExtension.class) annotation for the class to tell spring that we are using mockito to mock the dependencies.
- We need an instance of the PostService with the annotation of @InjectMocks to tell spring inject those mock objects in the constructor of PostService.
- The are two approaches we can take:

1. First approach, but might be too much: mock what repository returned and check if my service method returned the same data
2. I am guaranteed that the repository method will work (because someone else created it). so all I need to do, is check if the repository.findAll() gets called when I call service.getAll().

```java

import static org.junit.jupiter.api.Assertions.assertEquals;
import static org.junit.jupiter.api.Assertions.assertTrue;

import java.util.Optional;

import org.junit.jupiter.api.Test;
import org.junit.jupiter.api.extension.ExtendWith;
import org.mockito.ArgumentCaptor;
import org.mockito.BDDMockito;
import org.mockito.InjectMocks;
import org.mockito.Mock;
import org.mockito.Mockito;
import org.mockito.junit.jupiter.MockitoExtension;
import org.modelmapper.ModelMapper;

import com.zad.postcodeapi.suburb.Suburb;
import com.zad.postcodeapi.suburb.SuburbCreateDTO;
import com.zad.postcodeapi.suburb.SuburbRepository;
import com.zad.postcodeapi.suburb.SuburbService;

@ExtendWith(MockitoExtension.class)
public class SuburbServiceTest {

	@Mock
	private SuburbRepository suburbRepository;

	@Mock
	private ModelMapper mapper;

	@InjectMocks
	private SuburbService underTest;

	@Test
	void getAll_ReturnsAllData() {
		underTest.getAll();
		Mockito.verify(suburbRepository).findAll();
		// We are verifying that suburbRepository was called with findAll method.
	}

	@Test
	void createSuburb_ValidSuburb_AddSuburbToDB() {

		// Creating DTO
		SuburbCreateDTO dto = new SuburbCreateDTO("Suburb Name", 6000, "Suburb Postcode");

		Suburb suburb = new Suburb("Suburb Name", 6000, "Suburb Postcode");

		// Now we need to mock what mapper does.
		// We are telling the fake mapper what to return if we gave it dto and Suburb.
		BDDMockito.given(mapper.map(dto, Suburb.class)).willReturn(suburb);

		this.underTest.createSuburb(dto);
		// This should trigger the mapper.
		// We just want to check that the repository was called with the right argument.

		ArgumentCaptor<Suburb> suburbArgument = ArgumentCaptor.forClass(Suburb.class);
		// We are capturing the argument that the repository was called with.

		// We need the capture() method to actually get access to that argument.

		// We are checking that the save method was called.
		// We are also capturing the argument that it was called with.
		Mockito.verify(suburbRepository).save(suburbArgument.capture());

		// We are checking if what save was called with is the same as our suburb.
		assertEquals(suburb, suburbArgument.getValue());

		// We gave it a dto but it should be called with a suburb not a dto.
	}

	@Test
	void deletById_ExistingId_ReturnsTrue() {

		Long id = 123l;
		Suburb suburb = new Suburb("Suburb Name", 6000, "Suburb Postcode");

		// Here we are saying that the suburb exists.
		BDDMockito.given(suburbRepository.findById(id)).willReturn(Optional.of(suburb));

		Boolean deleted = this.underTest.deleteById(id);

		// capture the argument from the repository method
		ArgumentCaptor<Suburb> suburbArg = ArgumentCaptor.forClass(Suburb.class);
		// check if the right repository method was called, if yes we know it worked
		// because we didn't write that code.
		Mockito.verify(suburbRepository).delete(suburbArg.capture());
		// check that deleted == true
		assertTrue(deleted);
		// check that the save method was called with the right stuff.
		assertEquals(suburb, suburbArg.getValue());
	}

}

```

# Authentication

- It's better to add logging.level.org.springframework.security=TRACE to application.properties so we are able to see a lot more in the console when it comes to what is happening under the hood.
