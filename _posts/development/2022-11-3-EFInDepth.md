---
layout: post
title: EF In depth
author: Faten Elhariry
categories: development
tags: development
---

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
  ##### HasPrincipalKey: Using an alternate unique key
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
##### table hierarchy tph vs tpt? 