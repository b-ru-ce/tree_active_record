#Active Record for tree struct
its just study project

## Feature list


## Developed feature list
THIS FEATURES NOT IMPLEMENTED
1. Standart Active record function
* Automated mapping between classes and tables, attributes and columns.
    ```ruby
    class Product < TreeActiveRecord::Base
    end
    ```
    
    The Product class is automatically mapped to the table named "products",
    which might look like this:
    
    ```sql 
    CREATE TABLE products (
     id bigint NOT NULL auto_increment,
     parent_id int,
     name varchar(255),
     PRIMARY KEY  (id)
    );
    ```
     

This would also define the following accessors: <tt>Product#name</tt> and
<tt>Product#name=(new_name)</tt>. And also methods for work with tree <tt>Product.parent</tt> 
and <tt>Product.child</tt>



* Associations between objects defined by simple class methods.
    ```ruby
    class Firm < TreeActiveRecord::Base
      has_many   :clients
      belongs_to :conglomerate
    end
    ```
   


* Validation rules that can differ for new or existing objects.
    ```ruby
    class Account < TreeActiveRecord::Base
      validates :subdomain, :name, :email_address, :password, presence: true
      validates :subdomain, uniqueness: true
      validates :terms_of_service, acceptance: true, on: :create
      validates :password, :email_address, confirmation: true, on: :create
    end
    ```

* Database abstraction through simple adapters.
    ```ruby
    # connect to SQLite3
    TreeActiveRecord::Base.establish_connection(adapter: 'sqlite3', database: 'dbfile.sqlite3')

    # connect to MySQL with authentication
    TreeActiveRecord::Base.establish_connection(
      adapter:  'mysql2',
      host:     'localhost',
      username: 'me',
      password: 'secret',
      database: 'activerecord'
    )
    ```
 2. Working with tree
 * Search by tree
    ```ruby
    class Department < TreeActiveRecord::Base
      has_many :sub_product
    end
   
    class StaffingItem < TreeActiveRecord::Base
      belongs_to :product
    end
   
    Department.tree 
    # return collection of tree starting from root element. Default deep == 3
   
    Department.tree.find('*Department/name=123', 'StaffingItem/321')  
    # return all trees where Department.name == '123' and one of child StaffingItem with id == 321
    
    Department.tree(deep: 3).find('Department/name=123', '*StaffingItem/321' ) 
    # return all trees where Department.name == '123' and one of child StaffingItem with id == 321
    # tree will start from StaffingItem
    
    tree = Department.find(123).tree
    # retrun tree starting from this element
    
    tree.find('StaffingItem/name=321')
    # find by current tree
  
    ```
    