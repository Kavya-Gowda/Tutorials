---
auto_validation: true
title: Create Table Persistence and Generate Data
description: Create table persistence and generate data for it.
primary_tag: products>sap-cloud-platform--abap-environment
tags: [  tutorial>beginner, topic>abap-development, products>sap-cloud-platform ]
time: 10
author_name: Merve Temel
author_profile: https://github.com/mervey45
---

## Prerequisites  
- You have created an SAP Cloud Platform ABAP environment trial user or
- You have created a developer user in an SAP Cloud Platform ABAP Environment system.
- You have downloaded Eclipse Photon or Oxygen and installed ABAP Development Tools (ADT). See <https://tools.hana.ondemand.com/#abap>.

## Details
### You will learn  
  - How to create an ABAP package
  - How to create a database table
  - How to create an ABAP class to generate data

In this tutorial, wherever XXX appears, use a number (e.g. 000).

---

[ACCORDION-BEGIN [Step 1: ](Create ABAP package)]
  1. Open ABAP Development Tools (ADT) and right-click on `ZLOCAL` and select **ABAP Package**.

      ![Create ABAP package](package.png)

  2. Create a new ABAP package:
     - Name: `ZTRAVEL_APP_XXX`
     - Description: `Package for travel XXX`

     Use your number instead of `XXX`.

     Click **Next >**.

      ![Create ABAP package](package2.png)

  3. Click **Next >**.

      ![Create ABAP package](package3.png)

  4. Click **Finish**.

      ![Create ABAP package](package4.png)


  5. Right-click on **Favorite Packages** and select **Add Package**.

      ![Create ABAP package](package5.png)

  6. Search for `ZTRAVEL_APP_XXX`, select it and click **OK**.

      ![Create ABAP package](package6.png)

[DONE]
[ACCORDION-END]

[ACCORDION-BEGIN [Step 2: ](Create database table)]
  1. Right-click on your package `ZTRAVEL_APP_XXX`, select **New** > **Other ABAP Repository Object**.

      ![Create database table](table.png)

  2. Search for database table, select it and click **Next >**.

      ![Create database table](table2.png)

  3. Create a new database table:

       - Name: `ZTRAVEL_XXX`
       - Description: `Database table for travel data XXX`

     Click **Next >**.

      ![Create database table](table3.png)

  4. Click **Finish**.

      ![Create database table](table4.png)

  5. Replace your code with following:

    ```ABAP
    @EndUserText.label : 'Database table for travel data XXX'
    @AbapCatalog.enhancementCategory : #NOT_EXTENSIBLE
    @AbapCatalog.tableCategory : #TRANSPARENT
    @AbapCatalog.deliveryClass : #A
    @AbapCatalog.dataMaintenance : #LIMITED
    define table ztravel_xxx
    {
      key client      : abap.clnt not null;
      key travel_id   : /dmo/travel_id not null;
      agency_id       : /dmo/agency_id;
      customer_id     : /dmo/customer_id;
      begin_date      : /dmo/begin_date;
      end_date        : /dmo/end_date;
      @Semantics.amount.currencyCode : 'ztravel_xxx.currency_code'
      booking_fee     : /dmo/booking_fee;
      @Semantics.amount.currencyCode : 'ztravel_xxx.currency_code'
      total_price     : /dmo/total_price;
      currency_code   : /dmo/currency_code;
      description     : /dmo/description;
      overall_status  : /dmo/overall_status;
      created_by      : syuname;
      created_at      : timestampl;
      last_changed_by : syuname;
      last_changed_at : timestampl;
    }   
    ```

  6. Save and activate.

      ![save and activate](activate.png)

     Now the dictionary tables are defined. The dictionary tables are the basis of our travel booking data model.
  
[DONE]
[ACCORDION-END]

[ACCORDION-BEGIN [Step 3: ](Create ABAP class)]
  1. Right-click on your package `ZTRAVEL_APP_XXX`, select **New** > **ABAP Class**.

      ![Create ABAP class](class.png)

  2. Create a new ABAP class:

     - Name: `ZCL_GENERATE_TRAVEL_DATA_XXX`
     - Description: `Class for generating travel data`

     Click **Next >**.

      ![Create ABAP class](class2.png)

  3. Click **Finish**.

      ![Create ABAP class](class3.png)

  4. Replace your code with following:

    ```ABAP
    CLASS zcl_generate_travel_data_xxx DEFINITION
     PUBLIC
     FINAL
     CREATE PUBLIC .

     PUBLIC SECTION.
       INTERFACES if_oo_adt_classrun.
     PROTECTED SECTION.
     PRIVATE SECTION.
    ENDCLASS.


    CLASS ZCL_GENERATE_TRAVEL_DATA_XXX IMPLEMENTATION.

     METHOD if_oo_adt_classrun~main.
       DATA:itab TYPE TABLE OF ztravel_xxx.

    *   read current timestamp
       GET TIME STAMP FIELD DATA(zv_tsl).

    *   fill internal travel table (itab)
       itab = VALUE #(
     ( travel_id = '00000022' agency_id = '070001' customer_id = '000077' begin_date = '20190624' end_date = '20190628' booking_fee = '60.00' total_price =  '750.00' currency_code = 'USD'
       description = 'mv' overall_status = 'A' created_by = 'MUSTERMANN' created_at = '20190612133945.5960060' last_changed_by = 'MUSTERFRAU' last_changed_at = '20190702105400.3647680'  )
     ( travel_id = '00000106' agency_id = '070005' customer_id = '000005' begin_date = '20190613' end_date = '20190716' booking_fee = '17.00' total_price = '650.00' currency_code = 'AFN'
       description = 'Enter your comments here' overall_status = 'A' created_by = 'MUSTERMANN' created_at = '20190613111129.2391370' last_changed_by = 'MUSTERMANN' last_changed_at = '20190711140753.1472620' )
     ( travel_id = '00000103' agency_id = '070010' customer_id = '000011' begin_date = '20190610' end_date = '20190714' booking_fee = '17.00' total_price = '800.00' currency_code = 'AFN'
       description = 'Enter your comments here' overall_status = 'X' created_by = 'MUSTERFRAU' created_at = '20190613105654.4296640' last_changed_by = 'MUSTERFRAU' last_changed_at = '20190613111041.2251330' )
    ).

    *   delete existing entries in the database table
       DELETE FROM ztravel_xxx.

    *   insert the new table entries
       INSERT ztravel_xxx FROM TABLE @itab.

    *   check the result
       SELECT * FROM ztravel_xxx INTO TABLE @itab.
       out->write( sy-dbcnt ).
       out->write( 'Travel data inserted successfully!').

     ENDMETHOD.
    ENDCLASS.          
    ```

  5. Save, activate and click `F9` to run your ABAP class.

      ![save and activate](activate2.png)

  6. Switch to your database table and press `F8` to see your data.

      ![Create ABAP class](class4.png)

     Now the dictionary tables are filled with data.

[DONE]
[ACCORDION-END]

[ACCORDION-BEGIN [Step 4: ](Test yourself)]

[VALIDATE_1]
[ACCORDION-END]
---
