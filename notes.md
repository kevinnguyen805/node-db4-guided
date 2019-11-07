# Data Modeling Notes

## Requirements

A client has hired you to build an API for managing `zoos` and the `animals` kept at each `zoo`. The API will be use for `zoos` in the _United States of America_, no need to worry about addresses in other countries.

For the `zoos` the client wants to record:

- name.
- address.

For the `animals` the client wants to record:

- name.
- species.
- list of all the zoos where they have resided.

Determine the database tables necessary to track this information.
Label any relationships between table.

## GOAL TODAY: LEARN A WORKFLOW GOING FROM REQUIREMENTS TO DATABASE SCHEMAS 


## What is Normalization => what makes a good data model?
- Captures ALL and ONLY the information the system needs
- reflects what the DATA LOOKS LIKE IN REALITY (from the point of view of the system)
- is flexible - can evolve with the system 
- guarantees 'data integrity' 
     - you can never have a foreign ID that doesn't have a match with a primary key 
- is driven by the way we access/use the data 

## Components
- entities (nouns: zoo, animals, species) - like a resource ===> defined by tables
- properties ==> defined by columns/fields
- relationships ====> defined by foreign keys (FK)

## WORKFLOW
- identify entities (real and transactional => ex. transaction can be an order bringing together time and product = third table)
- identify the properties 
- identify relationships


## Mantras (things to repeat in your head)
- every table must have a **Primary Key**

1. put ID (primary keys) into each table
2. Identify how the tables are related with each other 
3. **varchar** is the best type of string field input 


## Relationships
- one to one
- one to many => most common; most systems you build are a collection of cascading *one-to-many* relationships
     *one-to-many relationships are modeled using a _foreign key_*
     *foreign keys ALWAYS go to the many side*
     *the foreign key column must be the same type (if primary key is a string - foreign key is a string // integer id? integer ID!) as the _PRIMARY KEY_ it references*
- many to many
     *many to many relationships are modeled using a third table*
     *third table represents some type of time stamp of when/how the two entities interacted*

# One to many
_there can be many animals in one species_
_one species can have many animals_

# Many to many
_an animal could have lived at more than one zoo_
_a zoo could have more than one animal_
## Constructing many to many table => THIRD TABLE IN THE MIDDLE SHOWING ONE TO MANY RELATIONSHIPS ON EITHER SIDES 
     * primary key
     * zoo_id -> pointing at zoos via foreign key 
     * animal_id -> pointing at animal via foreign key 
     * date animal started staying at zoo (not required)
     * date animal left the zoo (not required)



## KNEX FOREIGN KEYS 
1. set up knex => knexfile.js
2. start



exports.up = function(knex){
     return knex.schema.createTable('species', tbl => {
          tbl.increments();                                     <-- the type of primary key is: integer without negative values (also called unsigned)
          tbl.string('name', 255).notNullable();
     })

     .createTable('animals', tbl => {
          tbl.increments();       
          tbl.string('name', 255).notNullable();
          tbl.integer('species_id')               <-- define our foreign key!!
               .unsigned()                        <-- unsigned = integer without negative values
               .notNullable()
               .references('id')
               .inTable('species')
               .onDelete('RESTRICT')               <--- about deleting the record from the primary key table => could be 'RESTRICT' , 'NO ACTION', 'SET NULL' 
               .onUpdate('CASCADE')               <--- about changing the value of the primary key => 
     })

     .createTable('zoos', tbl => {
          tbl.increments()
          tbl.string('name', 255).notNullable()
          tbl.string('address', 255)
     })

     .createTable('animal_zoos', tbl => {
          tbl.increments()
          tbl.integer('zoo_id')
               .unsigned()
               .notNullable()
               .references('id')
               .inTable('zoos')
               .onDelete('RESTRICT')
               .onUpdate('CASCADE')

          tbl.integer('animal_id')
               .unsigned()
               .notNullable()
               .references('id')
               .inTable('animals')
               .onDelete('RESTRICT')
               .onUpdate('CASCADE')
         
     })

}



## What should happen if i delete a record for species that has animals?
* If I delete a species - all the animals associated should be deleted => REFLECTS REALITY!!!
*  
