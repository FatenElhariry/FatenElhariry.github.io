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
  <img src="../../../images/development/EF/1.png" />

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
      <img src="../../../images/development/EF/2.png" />
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