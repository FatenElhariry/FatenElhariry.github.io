---
layout: post
title: EF In depth
author: Faten Elhariry
categories: development
tags: development
---

_all mentioned topics will be implemented in this [demo](https://github.com/FatenElhariry/EFInDepth)_

#### What type of configuration run at the first time the dbcontext used 
   first time you use the application’s DbContext where are three configuration approaches: By Convention, Data Annotations, and the Fluent API will be discovered 
    - convention 
      like `Id, NavigationId` those properties will be used as primary key and foreign key 
    - by annotation 
      using `System.ComponentModel.DataAnnotations` to add annotation like `required, maxlength` and more 
    - by fluent API 
      like overried the configuration on DbContext 'OnModelCreating'

##### Value conversions: 
  - **Changing data to/from the database and Enum to string in db**
    ```
    var utcConverter = new ValueConverter<DateTime, DateTime>(toDb => toDb, fromDb => DateTime.SpecifyKind(fromDb, DateTimeKind.Utc));     
 
    modelBuilder.Entity<Product>()                
        .Property(e => e.CreatedAt)                 
        .HasConversion(utcConverter);


    modelBuilder.Entity<Product>()
                .Property(e => e.ProductType)
                .HasConversion<string>();
    ```
  - **more reference need to be revisied**
    - [value conversion](https://learn.microsoft.com/en-us/ef/core/modeling/value-conversions?tabs=data-annotations) 
    - [Value Comparers](https://learn.microsoft.com/en-us/ef/core/modeling/value-comparers?tabs=ef5)
##### You want to map an entity class to a read-only SQL View
  To set an entity class explicitly as read-only, you can use the fluent API HasNoKey() command or apply the attribute [Keyless] to the entity class. And if your entity class doesn’t have a primary key, you must mark it as read-only, using either of the two approaches
  ```
    modelBuilder.Entity<MyEntityClass>()
    .ToView("MyView");
  ```
  - **Index type**
  <img src="../../../../../images/development/EF/1.png" />

  - **more configuration over index**
    ```
      modelBuilder.Entity<MyClass>()
      .HasIndex(p => p.MyProp)
      .IsUnique()
      .HasFilter(“NOT SoftDeleted");
    ```

  - **Define the table name**
    ```
      [Table("XXX")]public class Book ... etc.


      modelBuilder.Entity<Book>().ToTable("XXX");
    ```
  - **Define the default scheme for table**
    ```
      modelBuilder.HasDefaultSchema("NewSchemaName");

      [Table("SpecialOrder", Schema = "sales")] class MyClass ... etc.

      modelBuilder.Entity<MyClass>() .ToTable("SpecialOrder", schema: "sales");

    ```
  - **Configuring the database column names in a table**
    ```
      [Column("SpecialCol")]public int BookId { get; set; }

      modelBuilder.Entity<MyClass>() .Property(b => b.BookId) .HasColumnName("SpecialCol");
    ```
  - **Query Filter(Using Global Query Filters in real-world situations)**
    enable the multitanant, and disappear the sotdelete items 
  - **Applying Fluent API commands based on the database provider type**
    - due to database provider difference we studied this information 
      The following listing is an example of applying the decimal to double type change if the database is SQLite. This code allows the Book App’s OrderBooksBy query object method to use an in-memory SQLite database.
    ```
      protected override void OnModelCreating ModelBuilder modelBuilder) {
          //... put your normal configration here
          if (Database.IsSqlite())               
          {                                     
              modelBuilder.Entity<Book>()        
                  .Property(e => e.Price)        
                  .HasConversion<double>();      
              modelBuilder.Entity<PriceOffer>()  
                  .Property(e => e.NewPrice)     
                  .HasConversion<double>();      
          }
      }
    ```
  - **Shadow properties: Hiding column data inside EF Core**
    - Shadow properties allow you to access database columns without having them appear in the entity class as a property.
    - Shadow properties allow you to “hide” data that you consider not to be part of the normal use of the entity class. 
    - This is all about good software practice: you let upper layers access only the data they need, and you hide anything that those layers don’t need to know about.
    - **Example**
      The tracking data you receive is separate from the primary use of the class, so you may decide to implement that data by using shadow properties, which can be picked up outside the entity class.
      ```
        modelBuilder.Entity<MyEntityClass>()
            .Property<DateTime>("UpdatedOn"); 
      ```
    - **Accessing shadow properties**
      ```
        var entity = new SomeEntityClass();         
        context.Add(entity);                        
        context.Entry(entity)                       
            .Property("UpdatedOn").CurrentValue     
                = DateTime.Now;                     
        context.SaveChanges();  
        // in order by 

        context.MyEntities
        .OrderBy(b => EF.Property<DateTime>(b, "UpdatedOn"))
        .ToList();
      ```
  - **Backing fields: Controlling access to data in an entity class**
    - As you saw earlier, columns in a database table are normally mapped to an entity class property with normal getters and setters
    - But you can also map a private field to your database **This feature is called a backing field**
    - it gives you more control of the way that database data is read or set by the software.
    - it also type of hidding but hide the data from the software this time _Like shadow properties, backing fields hide data_
    - **Examples**
      - _Hiding sensitive data_ —Hiding a person’s date of birth in a private field and making their age in years available to the rest of the software. 
      - Catching changes —Detecting an update of a property by storing the data in a private field and adding code in the setter to detect the update of a property. You will use this technique in chapter 12, when you use property change to trigger an event.
      - Creating Domain-Driven Design (DDD) entity classes —Creating DDD entity classes in which all the entity classes’ properties need to be read-only. Backing fields allow you to lock down navigational collection properties
      <img src="../../../../../images/development/EF/2.png" />
      ```
        EF.Property<DateTime>(entity, "_dateOfBirth") 

        modelBuilder.Entity<Person>()
          .Property("_dateOfBirth")
          .HasColumnName("DateOfBirth");
      ```
    - **Applying value converter to any DateTime property ending in "Utc"**
      ```
        protected override void                                              
        OnModelCreating(ModelBuilder modelBuilder)                           
        {
            var utcConverter = new ValueConverter<DateTime, DateTime>(       
                toDb => toDb,                                                
                fromDb =>                                                    
                    DateTime.SpecifyKind(fromDb, DateTimeKind.Utc));         
        
            foreach (var entityType in modelBuilder.Model.GetEntityTypes())  
            {
                foreach (var entityProperty in entityType.GetProperties())   
                {
                    if (entityProperty.ClrType == typeof(DateTime)           
                        && entityProperty.Name.EndsWith("Utc"))              
                    {                                                        
                        entityProperty.SetValueConverter(utcConverter);      
                    }     
                    if (entityProperty.ClrType == typeof(decimal)          
                        && entityProperty.Name.Contains("Price"))          
                    {                                                      
                        entityProperty.SetPrecision(9);                    
                        entityProperty.SetScale(2);                        
                    }                                                      
            
                    if (entityProperty.ClrType == typeof(string)           
                        && entityProperty.Name.EndsWith("Url"))            
                    {                                                      
                        entityProperty.SetIsUnicode(false);                
                    }                                                            
                    //... other examples left out for clarity
                }
            }
      ```

###### Dbcontext filters before update or create
  for enable soft delete in entityframework we did it in the modelCreating method to ensure we add user id that perform this action 
  ```
      /// Enable soft delete 
      foreach (var entityType in modelBuilder.Model.GetEntityTypes())
      {
          
          //other property code removed for clarity

          if (typeof(ISoftDelete)
          .IsAssignableFrom(entityType.ClrType))

              entityType.AddSoftDeleteQueryFilter(MyQueryFilterTypes.SoftDelete);

          if (typeof(IUserId).IsAssignableFrom(entityType.ClrType))
              entityType.AddSoftDeleteQueryFilter(MyQueryFilterTypes.UserId /*, IUserId*/);
          
      }
  ```
  - class for handle this action 
    ```
      public enum MyQueryFilterTypes { SoftDelete, UserId }
    public static class SoftDeleteQueryExtensions
    {
        public static void AddSoftDeleteQueryFilter(this IMutableEntityType entityData, MyQueryFilterTypes queryFilterType, IUserId userIdProvider = null)
        {
            var methodName = $"Get{queryFilterType}Filter";

            var methodToCall = typeof(SoftDeleteQueryExtensions)
                .GetMethod(methodName, BindingFlags.NonPublic | BindingFlags.Static)
                .MakeGenericMethod(entityData.ClrType);

            var filter = methodToCall.Invoke(null, new object[] { userIdProvider});

            entityData.SetQueryFilter((LambdaExpression)filter);
            
            if (queryFilterType == MyQueryFilterTypes.SoftDelete)
                entityData.AddIndex(entityData.FindProperty(nameof(ISoftDelete.SoftDeleted)));

            if (queryFilterType == MyQueryFilterTypes.UserId)
                entityData.AddIndex(entityData.FindProperty(nameof(IUserId.UserId)));
        }

        private static LambdaExpression GetUserIdFilter<TEntity>(IUserId userIdProvider) where TEntity : class, IUserId
        {
            Expression<Func<TEntity, bool>> filter = x => x.UserId == userIdProvider.UserId;
            return filter;
        }

        private static LambdaExpression GetSoftDeleteFilter<TEntity>( IUserId userIdProvider) where TEntity : class, ISoftDelete
        {
            Expression<Func<TEntity, bool>> filter = x => !x.SoftDeleted;
            return filter;
          }
      } 
    ```
#### Relationship   
  - **Principal key Vs alternate key**
    Principal key —A new term, taken from EF Core’s documentation, that refers to either the primary key, defined in part 1, or the new alternate key, which has a unique value per row and isn’t the primary key
  - **Principal entity**
    The entity that contains the principal-key properties, which the dependent relationship refer to via a foreign key(s)
  - **Dependent entity**
    The entity that contains the foreign-key properties that refer to the principal entity
  - **Principal key**
    the entity has a principal key, also known as the primary key, which is unique for each entity stored in the database
  - **Navigational property**
    A term taken from EF Core’s documentation that refers to the property containing a single entity class, or a collection of entity classes, that EF Core uses to link entity classes
  - **Required relationship**
    A relationship in which the foreign key is non-nullable (and principal entity must exist)
  - **Optional relationship**
    A relationship in which the foreign key is nullable (and principal entity can be missing)
  - **navigation properties**
    navigational property is useful, and some navigational properties aren’t. It is good practice to provide only navigational properties that make sense from the business or software design point of view.
    _Example_
      But do you need a fully defined relationship? From the software design point of view, there are two questions about the Book/Review navigational relationships. The answers to these questions define which navigational relationship you need to include:
      - Does the Book entity class need to know about the Review entity classes? I say yes, because we want to calculate the average review score.
      - Does the Review entity class need to know about the Book entity class? I say no, because in this example application, we don’t do anything with that relationship.
      _My experience is you should add a navigational property only when it makes sense from a business point of view or when you need a navigational property to create (EF Core’s Add) an entity class with a relationship (see section 6.2.1). Minimizing navigational properties will help make the entity classes easier to understand, and more-junior developers won’t be tempted to use relationships that aren’t right for your project._
  - **Configuring relationships By Convention**
    - In this case, you have a public property of type DbSet<Book>, which passed the “must have a valid primary key” test in that it has a public property called BookId.
    - If two navigational properties exist between the two entity classes, the relationship is known as fully defined
    - EF Core can work out By Convention whether it’s a one-to-one or a one-to-many relationship. If only one navigational property exists, EF Core can’t be sure, **so it assumes a one-to-many relationship**.
    - Certain one-to-one relationships may need configuration via the Fluent API if you have only one navigational property or if you want to change the default By Convention setting, such as when you’re deleting an entity class with a relationship
  - **Nullability of foreign keys: Required or optional dependent relationships**
    - defines whether the relationship is required (non-nullable foreign key) or optional (nullable foreign key)
    - For a required relationship, EF Core sets the OnDelete action to Cascade. If the principal entity is deleted, the dependent entity will be deleted too.
    - For a optional relationship, EF Core sets the OnDelete action to ClientSetNull. If the dependent entity is being tracked, the foreign key will be set to null when the principal entity is deleted. But if the dependent entity isn’t being tracked, the database constraint delete setting takes over, and the ClientSetNull setting sets the database rules as though the Restrict setting were in place. The result is that the delete fails at the database level, and an exception is thrown.
  - My unit tests show that one-to-one relationships are rejected if there is no foreign key to link the two entities. Therefore, EF Core’s By Convention won’t set up shadow property foreign keys on one-to-one relationships automatically. **shadow properties becasue there is not navigation property for this relation**
  - If you want to add a foreign key as a shadow property, you can do that via the Fluent API
#### When does By Convention configuration not work?
  - You have composite foreign keys
  - You want to create a one-to-one relationship without navigational links going both ways
  - You want to override the default delete-behavior setting
  - You have two navigational properties going to the same class
  - You want to define a specific database constraint
  - **InverseProperty**
    The Librarian and the borrower of the book (OnLoanTo navigational property) are both represented by the Person entity class. The Librarian navigational property and the OnLoanTo navigational property both link to the same class, and EF Core can’t set up the navigational linking without help. The InverseProperty Data Annotation shown in the following listing provides the information to EF Core when it’s configuring the navigational links.
    ```
      public class LibraryBook
      {
          public int LibraryBookId { get; set; }
          public string Title { get; set; }
      
          public int LibrarianPersonId { get; set; }
          public Person Librarian { get; set; }
      
          public int? OnLoanToPersonId { get; set; }
          public Person OnLoanTo { get; set; }
      }

      public class Person
      {
          public int PersonId { get; set; }
          public string Name { get; set; }
      
          [InverseProperty("Librarian")]      
          public ICollection<LibraryBook> 
              LibrarianBooks { get; set; }
      
          [InverseProperty("OnLoanTo")]       
          public ICollection<LibraryBook> 
              BooksBorrowedByMe { get; set; }
      }
    ```
    _This code is one of those configuration options that you rarely use, but if you have this situation, you must either use it or define the relationship with the Fluent API. Otherwise, EF Core will throw an exception when it starts, as it can’t work out how to configure the relationships._
  ##### 8.6 Fluent API relationship configuration commands
    the Fluent API has a well-designed set of commands that cover all the possible combinations of relationships. It also has extra commands that allow you to define other database constraints.
    <img src="../../../../images/development/EF/3.png" />

    - **Creating a one-to-one relationship**
      One-to-one relationships can get a little complicated because there are three ways to build them in a relational database. To understand these options, you’ll look at an example in which you have attendees (entity class Attendee) at a software convention, and each attendee has a unique ticket (entity class Ticket).
      -  The principal entities are at the top of the diagram, and the dependent entities are at the bottom.
      - The three ways of defining a one-to-one relationship in a relational database
          <img src="../../../../images/development/EF/4.png" />
        - illustrate it with ef fluent api 
          <img src="../../../../images/development/EF/5.png" />
        Options 2 and 3 in figure 8.5 turn the principal/dependent relationship around, with the Attendee becoming the principal entity in the relationship. This situation swaps the required/optional nature of the relationship. Now the Attendee can exist without the Ticket, but the Ticket can’t exist without the Attendee. Options 2 and 3 do enforce the assignment of a Ticket to only one Attendee, but replacing Ticket with another Ticket instance requires you to delete the old ticket first
      - options 2, 3 
        <img src="../../../../images/development/EF/6.png" />
    - **Creating a one-to-many relationship**
      - One-to-many relationships are simpler
      - the many entities contain the foreign-key value. You can define most one-to-many relationships with the By Convention approach simply by giving the foreign key in the many entities a name that follows the By Convention approach 
      <img src="../../../../images/development/EF/7.png" />
      - Collections have a couple of features that are worth knowing about. First, you can use any generic type for a collection that implements the IEnumerable<T> interface, such as IList<T>, Collection<T>, HashSet<T>, List<T>, and so on. **IEnumerable<T> on its own is a special case, as you can’t add to that collection**.
      - For performance reasons, you should use HashSet<T> for navigational collections, because it improves certain parts of EF Core’s query and update processes.
      - But HashSet doesn’t guarantee the order of entries, which could cause problems if you add sorting to your Includes **it used the hash code to define the owrder of each property**
      - That’s why I recommend in part 1 and 2 using ICollection<T> if you might sort your Include methods, as ICollection preserves the order in which entries are added. But in part 3, which is about performance, you don’t use sort in Includes so that you can use HashSet<T> for better performance.
      - You can provide a getter only if you initialize the backing field with an empty collection. The following is also valid:
      ```
        public ICollection<Review> Reviews { get; } = new List<Review>();

      ```
      _Although initializing the collection might make things easier in this case, I don’t recommend initializing a navigational collection property. I have given my reasons for not initializing collection navigational properties in section 6.1.6._
    - **Creating a many-to-many relationship**
      -  you learned about the two types of many-to-many relationships:
        - Your linking table contains information that you want to access when reading in the data on the other side of the many-to-many relationship. An example is the Book to Author many-to-many relationship, in which the linking table contains the order in which the Author Names should be shown.
      - You directly access the other side of the many-to-many relationship. An example is the Book to Tag many-to-many relationship, in which you can directly access the Tags collection in the Book entity class without ever needing to access the linking table.
      - In the Book/Author example, the By Convention configuration can find and link all the scalar and navigational properties so that the only configuration required is setting up the primary key. The following code snippet uses Fluent API in the application’s DbContext’s OnModelCreating method:
      ```
        protected override void OnModelCreating(ModelBuilder modelBuilder) 
        {
            modelBuilder.Entity<BookAuthor>() 
                  .HasKey(x => new {x.BookId, x.AuthorId}); 
        }
      ```
      - You can configure the four relationships in the many-to-many relationship by using the Fluent API with the code in the following listing. Note that the HasOne/WithMany Fluent API commands 
      ```
        public static void Configure(this EntityTypeBuilder<BookAuthor> entity)
        {
            entity.HasKey(p => 
                new { p.BookId, p.AuthorId });    
            //-----------------------------
            //Relationships
            entity.HasOne(p => p.Book)            
                .WithMany(p => p.AuthorsLink)      
                .HasForeignKey(p => p.BookId);    
            entity.HasOne(p => p.Author)          
                .WithMany(p => p.BooksLink)        
                .HasForeignKey(p => p.AuthorId);  
        }
      ```
    - **type two of many-to-many relation in ef(direct access to the collection navigation)**
      <img src="../../../../images/development/EF/8.png" />
      _But if you want to add your own linking table and configuration, you can do that via Fluent API configuration. The entity class for the linking table is similar to the BookAuthor linked entity class_
    - **Configuring direct many-to-many relationships using Fluent API** 
      ```
        public void Configure(EntityTypeBuilder<Book> entity)
        {
            //... other configrations left out for clarity
        
            entity.HasMany(x => x.Tags)                              
                .WithMany(x => x.Books)                              
                .UsingEntity<BookTag>(                               
                    bookTag => bookTag.HasOne(x => x.Tag)            
                        .WithMany().HasForeignKey(x => x.TagId),     
                    bookTag => bookTag.HasOne(x => x.Book)           
                        .WithMany().HasForeignKey(x => x.BookId));   
        }
      ```
      - The HasMany/WithMany sets up a direct many-to-many relationship.
      - The UsingEntity<T> method allows you to define an entity class for the linking table.
      - Defined Tag side of the many-to-many relationship, Defined Book side of the many-to-many relationship
      [more about many-to-many](https://www.youtube.com/watch?v=BIImyq8qaD4&list=PLdo4fOcmZ0oVWop1HEOml2OdqbDs6IlcI)
      ```
      public class Book
      {
          private readonly ICollection<Review> _reviews = new List<Review>();                                 

          public int BookId { get; set; }
          public string Title { get; set; }
          //... other properties/relationships left out
      
          public double? ReviewsAverageVotes { get; private set; }  
      
          public IReadOnlyCollection<Review> Reviews => _reviews.ToList();                                     

          public void AddReview(Review review)                       
          {
              _reviews.Add(review);                                  
              ReviewsAverageVotes =  _reviews.Average(x => x.NumStars);                 
          }
          public void RemoveReview(Review review)                    
          {
              _reviews.Remove(review);                               
              ReviewsAverageVotes = _reviews.Any() ? _reviews.Average(x => x.NumStars) : (double?)null;                                   
          }
      }
      ```
      - You add a backing field, which is a list. By default, EF Core will read and write to this field.
      - Holds a precalculated average of the reviews and is read-only, Returns a copy of the reviews in the _reviews backing field
      - Adds a method to allow a new Review to be added to the _reviews collection
      - Adds the new review to the backing field _reviews and updates the database on the call to SaveChanges
      - Recalculates the average votes for the book,If there are any reviews, recalculates the average votes for the book
      _You didn’t have to configure the backing field because you were using By Convention naming, and by default, EF Core reads and writes data to the _reviews field._
    - **Additional methods available in Fluent API relationships**
      - **OnDelete—Changes the delete action of a dependent entity**
        ```
          entity.HasOne(p => p.ChosenBook)
              .WithMany()
              .OnDelete(DeleteBehavior.Restrict); 
        ```
        _Adds the OnDelete method to the end of defining a relationship_
        <img src="../../../../images/development/EF/10.png" />
      - IsRequired—Defines the nullability of the foreign key
      - HasPrincipalKey—Uses an alternate unique key
      - HasConstraintName—Sets the foreign-key constraint name and MetaData access to the relationship data
  - **Deleting a principal entity with an optional dependent entity**
  ```
  var entity = context.DeletePrincipals          
    .Include(p => p.DependentDefault)          
    .Single(p => p.DeletePrincipalId == 1);
  
  context.Remove(entity);                        
  context.SaveChanges(); 
  ```
  _Note that if you don’t include the Include method or another way of loading the optional dependent entity, SaveChanges will throw a DbUpdateException because the database server will have reported a foreign-key constraint violation. One way to align EF Core’s approach to an optional relationship with the database server’s approach is to set the delete behavior to SetNull instead of the default ClientSetNull, making the foreign-key constraint in the database ON DELETE SET NULL (SQL Server) and putting the database in charge of setting the foreign key to null. Whether or not you load the optional dependent entity, the outcome of the called SaveChanges will be the same: the foreign key on the optional dependent entity will be set to null._
- **IsRequired: Defining the nullability of the foreign key**
  - defines whether the relationship is required or optional.
  - The IsRequired method is most useful in shadow properties because EF Core makes shadow properties nullable by default, and the IsRequired method can change them to non-nullable.
  - one-to-one relationships that use shadow properties for their foreign keys.
    ```
      public class Attendee
      {
          public int AttendeeId { get; set; }
          public string Name { get; set; }
      
          public int TicketId { get; set; }                  
          public Ticket Ticket { get; set; }                
          // One-to-one navigational property using a shadow property for the foreign key. By default, the foreign key is nullable, so the relationship is optional
          public MyOptionalTrack Optional { get; set; }     
          // One-to-one navigational property using a shadow property for the foreign key. You use Fluent API commands to say that the foreign key isn’t nullable, so the relationship is required
          public MyRequiredTrack Required { get; set; }     
      }
    ```
    _The Optional navigational property, which uses a shadow property for its foreign key, is configured by convention, which means that the shadow property is left as a nullable value. _
    -  and if the Attendee entity is deleted, the MyOptionalTrack entity isn’t deleted.
    - you use the IsRequired method to make the Required one-to-one navigational property, ach Attendee entity must have a MyRequiredTrack entity assigned to the Required property.
    - **The Fluent API configuration of the Attendee entity class**
      ```
        public void Configure(EntityTypeBuilder<Attendee> entity)
      {
          entity.HasOne(attendee => attendee.Ticket)     
              .WithOne(attendee => attendee.Attendee)
              .HasForeignKey<Attendee>
                  (attendee => attendee.TicketId)        
              .IsRequired();
      
          entity.HasOne(attendee => attendee.Required)   
              .WithOne(attendee => attendee.Attend)
              .HasForeignKey<Attendee>("MyShadowFk")                          
              .IsRequired();                             
      }
      ```
      _Uses the HasForeignKey<T> method, which takes a string because it’s a shadow property and can be referred to only via a name. Note that you use your own name., The string parameter of the HasForeignKey<T>(string) method allows you to define the shadow foreign-key property name._
  - **HasPrincipalKey: Using an alternate unique key**
    - it is a unique value but not the primary key
    - I gave an example of an alternate key called UniqueISBN, which represents a unique key that isn’t the primary key
    - Example 
       a Person entity class, which uses a normal int primary key, but you’ll use the UserId as an alternate key when linking to the person’s contact information
       ```
        public class Person
        {
            public int PersonId { get; set; }
        
            public string Name { get; set; }
            // Holds the person’s unique Id
            public Guid UserId { get; set; }                
            // Navigational property linking to the ContactInfo
            public ContactInfo ContactInfo { get; set; }    
        }

        public class ContactInfo
        {
          public int ContactInfoId { get; set; }

          public string MobileNumber { get; set; }
          public string LandlineNumber { get; set; }

          public Guid UserIdentifier { get; set; }     
        }
       ```
      - the alternate key in the Person entity class as a foreign key in the ContactInfo entity class.
      <img src="../../../../images/development/EF/11.png" />
      - You can have composite alternate keys, which are made up of two or more properties. You handle them in the same way that you do composite keys: by using an anonymous Type, such as
      ```
      HasPrincipalKey<MyClass>(c => new {c.Part1, c.Part2}).
      ```
      - Unique keys ensure that each entry is unique; they can’t be used in a foreign key.
      - Unique keys can be null, but alternate keys can’t.
      - Unique key values can be updated, but alternate keys can’t. (See EF Core issue [#4073 at](http://mng.bz/vzEM)
      - You can define a property as a standalone alternate key by using the Fluent API command, but you don’t need to do that, because using the HasPrincipalKey method to set up a relationship automatically registers the property as an alternate key.
      ```modelBuilder.Entity<Car>().HasAlternateKey(c => c.LicensePlate)```
  - **HasConstraintName SETTING THE FOREIGN-KEY CONSTRAINT NAME**
    allows you to set the name of the foreign-key constraint, which can be useful if you want to catch the exception on foreign-key errors and use the constraint name to form a more user-friendly error message [details](https://www.thereformedprogrammer.net/entity-framework-core-validating-data-and-catching-sql-errors/)
  - **METADATA: ACCESS TO THE RELATIONSHIP INFORMATION**
    property provides access to the relationship data, some of which is read/write. Much of what the MetaData property exposes can be accessed via specific commands, such as IsRequired, but if you need something out of the ordinary, look through the various methods/properties supported by the MetaData property.
##### Alternative ways of mapping entities to database tables
  Sometimes, it’s useful to not have a one-to-one mapping from an entity class to a database table. Instead of having a relationship between two classes, you might want to combine both classes into one table.
  - This approach allows you to load only part of the table when you use one of the entities, which will improve the query’s performance.
  - **wned types** 
    Allows a class to be merged into the entity class’s table and is useful for using normal classes to group data.
    - which allow you to define a class that holds a common grouping of data, such as an address or audit data, that you want to use in multiple places in your database. The owned type class doesn’t have its own primary key, so it doesn’t have an identity of its own; it relies on the entity class that “owns” it for its identity. In DDD terms, owned types are known as value objects.
    - the data in an owned type can be configured to be saved in a separate, hidden table.
    - _Here are two ways of using owned types:_
      - The owned type data is held in the same table that the entity class is mapped to.
      - The owned type data is held in a separate table from the entity class.
      - **Example**
        OrderInfo that needs two addresses: BillingAddress and DeliveryAddress. These addresses are provided by the Address class
        You can mark an Address class as an owned type by adding the attribute [Owned] to the class. An owned type has no primary key
        ```
        public class OrderInfo                             
        {
            public int OrderInfoId { get; set; }
            public string OrderNumber { get; set; }
        
            public Address BillingAddress { get; set; }   
            public Address DeliveryAddress { get; set; }   
        }
        
        [Owned]                                           
        public class Address                              
        {
            public string NumberAndStreet { get; set; }
            public string City { get; set; }
            public string ZipPostCode { get; set; }
            [Required]                                  
            [MaxLength(2)]                              
            public string CountryCodeIso2 { get; set; } 
        } 
        ```
        - Two distinct Address classes. The data for each Address class will be included in the table that the OrderInfo is mapped to.
        - The attribute [Owned] tells EF Core that it is an owned type., An owned type has no primary key.
        - in this case you don’t need use the Fluent API to configure the owned type
        - This approach saves you time, especially if your owned type is used in many places, because you don’t have to write the Fluent API configuration. But if you don’t want to use the [Owned] attribute
        - The Fluent API to configure the owned types within OrderInfo
          ```
            public class SplitOwnDbContext: DbContext
            {
                public DbSet<OrderInfo> Orders { get; set; }
                //... other code removed for clarity
            
                protected override void OnModelCreating (ModelBuilder modelBuilder)
                {
                  modelBulder.Entity<OrderInfo>()       
                      .OwnsOne(p => p.BillingAddress);  

                  modelBulder.Entity<OrderInfo>(DeliveryAddress);                            
                  
                }
            }
          ```
          _Uses the OwnsOne method to tell EF Core that property BillingAddress is an owned type and that the data should be added to the columns in the table that the OrderInfo maps to_
          **The result is a table containing the two scalar properties in the OrderInfo entity class, followed by two sets of Address class properties, one prefixed by BillingAddress_ and another prefixed by DeliveryAddress_. Because an owned type property can be null, all the properties are held in the database as nullable columns.**
        - The fact that the owned type property can be null means that owned types within an entity class are a good fit for what DDD calls a value object. A value object has no key, and two value objects with the same properties are considered to be equal. The fact that they can be null allows for an “empty” value object.
        - you can set it as required with convension or with fluent api like this 
          ```
          modelBulder.Entity<OrderInfo>()
              .OwnsOne(p => p.BillingAddress);
          modelBulder.Entity<OrderInfo>()     
              .OwnsOne(p => p.DeliveryAddress);
          modelBulder.Entity<OrderInfo>()      
          .Navigation(p => p.DeliveryAddress)   
              .IsRequired(); 
          ```
        -  are automatically created and filled with data when you read the entity. There’s no need for an Include method or any other form of relationship loading.
        - Owned types can be nested. You could create a CustomerContact owned type, which in turn contains an Address owned type, for example. If you used the CustomerContact owned type in another entity class—let’s call it SuperOrder—all the CustomerContact properties and the Address properties would be added to the SuperOrder’s table.
    - **OWNED TYPE DATA IS HELD IN A SEPARATE TABLE FROM THE ENTITY CLASS**
      you’ll create a User entity class that has a property called HomeAddress of type Address. In this case, you add a ToTable method after the OwnsOne method in your configuration code.
      ```
      public class SplitOwnDbContext: DbContext
      {
          public DbSet<OrderInfo> Orders { get; set; }
          //... other code removed for clarity
      
          protected override void OnModelCreating(ModelBuilder modelBuilder)
          {
            modelBulder.Entity<User>()
                .OwnsOne(p => p.HomeAddress);
                .ToTable("Addresses");        
          }
      }
      ```
      _ tells EF Core to store the owned type, Address, in a separate table, with a primary key equal to the primary key of the User entity that was saved to the database._

  - **Table per hierarchy (TPH)** 
    Allows a set of inherited classes to be saved in one table, such as classes called Dog, Cat, and Rabbit that inherit from the Animal class.
    stores all the classes that inherit from one another in a single database table
    ```
      public class PaymentCash
      {
          [Key]
          public int PaymentId { get; set; }
          public decimal Amount { get; set; }
      } 
      
      //PaymentCredit - inherits from PaymentCash
      public class PaymentCard : PaymentCash
      {
          public string ReceiptCode { get; set; }
      }

      // in db context and there is no other configuration needed  
       //Table-per-hierarchy
      public DbSet<PaymentCash> CashPayments { get; set; }
      public DbSet<PaymentCard> CreditPayments { get; set; }

    ```
    _the output is_
    ```
      CREATE TABLE [CashPayments] (
          [PaymentId] int NOT NULL IDENTITY,
          [Amount] decimal(18, 2) NOT NULL,
          [Discriminator] nvarchar(max) NOT NULL,   
          [ReceiptCode] nvarchar(max),              
          CONSTRAINT [PK_CashPayments] PRIMARY KEY ([PaymentId])
      );
    ```
    **As you can see, EF Core has added a Discriminator column, which it uses when returning data to create the correct type of class: PaymentCash or PaymentCard, based on what was saved. Also, the ReceiptCode column is filled/read only if the class type is PaymentCard.**
    - Any scalar properties not in the TPH base class are mapped to nullable columns because those properties are used by only one version of the TPH’s classes
    - more useful way 
      <img src="../../../../images/development/EF/12.png" />
      - This approach is much more useful because now you can place a Payment abstract class in the SoldIt entity class and get the amount and type of payment, whether it’s cash or a card. 
      - The PType property tells you the type (the PType property is of type PTypes, which is an enum with values Cash or Card), and if you need the Receipt property in the PaymentCard, you can cast the Payment class to the type PaymentCard.
      ```
        public class Chapter08DbContext : DbContext
        {
            //... other DbSet<T> properties removed 
            // List of sold items, with a required link to Payment
            public DbSet<Payment> Payments { get; set; }     
            public DbSet<SoldIt> SoldThings { get; set; }    
        
            public Chapter08DbContext(DbContextOptions<Chapter08DbContext> options): base(options)
            { }
        
            protected override void OnModelCreating(ModelBuilder modelBuilder)
            {
                //... other configurations removed
                modelBuilder.Entity<Payment>()
                  .HasDiscriminator(b => b.PType)           
                  .HasValue<PaymentCash>(PTypes.Cash)       
                  .HasValue<PaymentCard>(PTypes.Card);      
            }
        }
      ```
      - The HasDiscriminator method identifies the entity as a TPH and then selects the property PType as the discriminator for the different types. In this case, it’s an enum, which you set to be bytes in size.
      - This example uses an abstract class as the base class, which I think is more useful, but it could just as well keep the original PaymentCash, with the PaymentCard inheriting from it. An abstract base class makes it easier to alter the common TPH properties.
      - how to deal with CRUD Operations 
      ```
        var sold = new SoldIt()
        {
            WhatSold = "A hat",
            Payment = new PaymentCash {Amount = 12}
        };
        context.Add(sold);
        context.SaveChanges();
      ```
      - Then EF Core saves the correct version of data for that type and sets the discriminator so that it knows the TPH class type of the instance.
      - When you read back the SoldIt entity you just saved, with an Include to load the Payment navigational property, the type of the loaded Payment instance will be the correct type (PaymentCash or PaymentCard), depending on what was used when you wrote it to the database. Also, in this example the Payment’s property PType, which you set as the discriminator, tells you the type of payment: Cash or Card.
      - When you query TPH data, the EF Core OfType<T> method allows you to filter the data 
      ```
        context.Payments.OfType<PaymentCard>()
      ```
      - You can also filter TPH classes in Includes. See this article for [more information](https://learn.microsoft.com/en-us/ef/core/querying/related-data/#include-on-derived-types)
  - **Table per type (TPT)** Maps each class to a different table. This approach works like TPH except that each class is mapped to a separate table.
  - which allows each entity class inherited from a base class to have its own table. This option is the opposite of the table per hierarchy (TPH) approach
  - TPH is better when each inherited class has a large common part and only a small amount of per-class data.

  - **Table splitting** Allows multiple entity classes to be mapped to the same table and is useful when some columns in a table are read more often than all the table columns.
  - The three classes used in the TPT example
    ```
    public abstract class Container             
    {
        [Key]
        public int ContainerId { get; set; }    
    
        public int HeightMm { get; set; }       
        public int WidthMm { get; set; }        
        public int DepthMm { get; set; }        
    }
    
    public class ShippingContainer : Container  
    {
        public int ThicknessMm { get; set; }    
        public string DoorType { get; set; }    
        public int StackingMax { get; set; }    
        public bool Refrigerated { get; set; }  
    }
    
    public class PlasticContainer : Container   
    {
        public int CapacityMl { get; set; }     
        public Shapes Shape { get; set; }       
        public string ColorARGB { get; set; }   
    }
    ```
    _The Container class is marked as abstract because it won’t be created_
    - The updates to the application’s DbContext to set up the TPT containers
      ```
      public class Chapter08DbContext : DbContext
      {
        public Chapter08DbContext(DbContextOptions<Chapter08DbContext> options): base(options)
        { }

        //... other DbSet<T> removed for clarity
        public DbSet<Container> Containers { get; set; }    

        protected override void OnModelCreating(ModelBuilder modelBuilder)
        {
            //... other configrations removed for clarity
            modelBuilder.Entity<ShippingContainer>()        
                .ToTable(nameof(ShippingContainer));        
            modelBuilder.Entity<PlasticContainer>()         
                .ToTable(nameof(PlasticContainer));         
        }
      ```
      _the magic comes when you query the DbSet<Container> Containers in the application’s DbContext, because it returns all the containers using the correct class type, ShippingContainer or PlasticContainer, for each entity returned._
      - Read all query — `context.Containers.ToList()`
        - This option reads in all the TPT types, and each entry in the list will be of the correct type (ShippingContainer or PlasticContainer) for the type it returns. This option is useful only if you want to list a summary of all the containers.
      - OfType query— `context.Containers.OfType<ShippingContainer>().ToList()`
        - This option reads in only the entries that are of the type ShippingContainer.
      - Set query— `context.Set<ShippingContainer>().ToList()`
        - This option returns only the ShippingContainer type (just like the OfType query), but the SQL is slightly more efficient than the OfType query.
  - **Table splitting: Mapping multiple entity classes to the same table**
      called table splitting, allows you to map multiple entities to the same table. This feature is useful if you have a large amount of data to store for one entity, but your normal queries to this entity need only a few columns
      -  will be quicker because you’re loading only a subsection of the whole entity’s data
      - It can also make updates quicker by splitting the table across two or more classes.
        <img src="../../../../images/development/EF/13.png" />
        ```
          public class SplitOwnDbContext : DbContext
          {
              //... other code removed
          
              protected override void OnModelCreating(ModelBuilder modelBuilder)
              {
                  modelBuilder.Entity<BookSummary>()   
                    .HasOne(e => e.Details)           
                    .WithOne()                        
                    .HasForeignKey<BookDetail>        
                          (e => e.BookDetailId);      
                  modelBuilder.Entity<BookSummary>()   
                    .ToTable("Books");                
                                                      
                  modelBuilder.Entity<BookDetail>()    
                    .ToTable("Books");                
              }
          }
        ```
        _two books as having a relationship in the same way that you’d set up a one-to-one relationship,  You must map both entity classes to the Books table to trigger the table splitting._
        - After you’ve configured the two entities as a table split, you can query the BookSummary entity on its own and get the summary parts or the other part or even include both.  
        - **some great notes**
          - You can update an individual entity class in a table split individually; you don’t have to load all the entities involved in a table split to do an update.
          - You’ve seen a table split to two entity classes, but you can table-split any number of entity classes.
  - **Property bags** —Allows you to create an entity class via a Dictionary, which gives you the option to create the mapping on startup. Property bags also use two other features: mapping the same type to multiple tables and using an indexer in your entity classes.
    _EF Core 5 added a feature called a property bag that uses a Dictionary<string, object> type to map to the database. A property bag is used to implement the direct many-to-many relationship feature, where the linking table had to be created at configuration time._
    - **A property bag uses two features**
      - The first feature is shared entity types, where the same type can be mapped to multiple tables
      - The second feature uses a C# indexer property in an entity class to access data, such as public object this[string key]
      - **Using a property-bag Dictionary to define a table on startup**
        ```
        public class PropertyBagsDbContext : DbContext
        {
            private readonly TableSpec _tableSpec;                    
        
            public PropertyBagsDbContext(DbContextOptions<PropertyBagsDbContext> options, TableSpec tableSpec): base(options)
            {
                _tableSpec = tableSpec;                               
            }
        
            public DbSet<Dictionary<string, object>> MyTable          
                => Set<Dictionary<string, object>>(_tableSpec.Name);  
        
            protected override void OnModelCreating(ModelBuilder modelBuilder)
            { 
                modelBuilder.SharedTypeEntity<Dictionary<string, object>>(_tableSpec.Name, b =>                         
                {
                    foreach (var prop in _tableSpec.Properties)       
                    {
                        var propConfig = b.IndexerProperty(           
                            prop.PropType, prop.Name);                
                        if (prop.AddRequired)                         
                            propConfig.IsRequired();                  
                    }
                }).Model.AddAnnotation("Table", _tableSpec.Name);     
            } 
        }
        ```
        _The DbSet called MyTable links to the SharedType entity built in OnModelCreating,Defines a SharedType entity type, which allows the same type to be mapped to multiple tables, You give this shared entity type a name so that you can refer to it._
        - TableSpec class must be the same every time because EF Core caches the configuration
        - The property-bag entity’s configuration is fixed for the whole time the application is running
        - To access the property-bag entity, you use the MyTable property 
        - **Adding and querying a property bag**
          ```
          var propBag = new Dictionary<string, object>     
          {
              ["Title"] = "My book",                       
              ["Price"] = 123.0                            
          };
          context.MyTable.Add(propBag);                    
          context.SaveChanges();                           
          
          var readInPropBag = context.MyTable.Single(x => (int)x["Id"] == 1);             
          
          var title = readInPropBag["Title"];    
          ```
        - A property bag’s property names follow By Convention naming. The primary key is "Id", for example. But you can override this setting with Fluent API commands as usual.
        - You can have multiple property bags. The SharedTypeEntity Fluent API method allows you to map the same type to different tables.
        - A property bag can have relationships to other classes or property bags. You use the HasOne/HasMany Fluent API methods, but you can’t define navigational properties in a property bag.
        - You don’t have to set every property in the dictionary when you add a property-bag entity. Any properties/columns not set will be set to the type’s default value.
##### table hierarchy tph vs tpt? 
