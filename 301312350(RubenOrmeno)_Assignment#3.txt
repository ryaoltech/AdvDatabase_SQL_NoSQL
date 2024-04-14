/*
Q1: Assignment : Create a procedure named STATUS_SHIP_SP that allows an employee in the Shipping Department to update an order status to add 
shipping information. The BB_BASKETSTATUS table lists events for each order so that a shopper can see the current status, date, 
and comments as each stage of the order process is finished. The IDSTAGE column of the BB_BASKETSTATUS table identifies each stage; 
the value 3 in this column indicates that an order has been shipped. The procedure should allow adding a row with an IDSTAGE of 3, date shipped,
tracking number, and shipper. The BB_STATUS_SEQ sequence is used to provide a value for the primary key column. 
Test the procedure with the following information:
Basket # = 3 Date shipped = 20-FEB-12 Shipper = UPS Tracking # = ZW2384YXK4957


*/



Create or replace procedure status_ship_sp (p_idbasket IN bb_basketstatus.idbasket%type, 
                                p_dateshipped IN bb_basketstatus.dtstage%type,
                                p_notes IN bb_basketstatus.notes%type,
                                p_shipper IN bb_basketstatus.shipper%type, 
                                p_trackingnumber IN bb_basketstatus.shippingnum%type) is
                                
BEGIN

insert into bb_basketstatus (IDSTATUS, IDBASKET, IDSTAGE, DTSTAGE, NOTES, SHIPPER, SHIPPINGNUM) VALUES (
    BB_STATUS_SEQ.NEXTVAL, p_idbasket, 3, p_dateshipped, p_notes, p_shipper, p_trackingnumber);


END;

--TESTING Q1
DECLARE
p_idbasket bb_basketstatus.idbasket%type := 3; 
p_dateshipped bb_basketstatus.dtstage%type := TO_DATE('20-FEB-12','DD-MON-YY');
p_notes bb_basketstatus.notes%type := 'Customer wants priority';
p_shipper bb_basketstatus.shipper%type := 'UPS'; 
p_trackingnumber bb_basketstatus.shippingnum%type := 'ZW2384YXK4957';
BEGIN

status_ship_sp(p_idbasket, p_dateshipped, p_notes, p_shipper, p_trackingnumber);

END;


/*
Q2: Assignment : Returning Order Status Information Create a procedure that returns the most recent order status information for a specified 
basket. This procedure should determine the most recent ordering-stage entry in the BB_BASKETSTATUS table and return the data. 
Use an IF or CASE clause to return a stage description instead of an IDSTAGE number, which means little to shoppers. 
The IDSTAGE column of the BB_BASKETSTATUS table identifies each stage as follows:
• 1—Submitted and received • 2—Confirmed, processed, sent to shipping • 3—Shipped • 4—Cancelled • 5—Back-ordered
The procedure should accept a basket ID number and return the most recent status description and date the status was recorded. 
If no status is available for the specified basket ID, return a message stating that no status is available. 
Name the procedure STATUS_SP. Test the procedure twice with the basket ID 4 and then 6.

*/


create or replace procedure STATUS_SP (
                                p_basketID IN bb_basketstatus.idstage%type,
                                p_stage_description OUT bb_basketstatus.notes%type,
                                p_date OUT bb_basketstatus.dtstage%type
                            ) IS
v_idstage bb_basketstatus.idstage%type := 0;

BEGIN
    
    select max(dtstage) into p_date from bb_basketstatus where idbasket = p_basketID;
    select idstage into v_idstage from bb_basketstatus where idbasket = p_basketID and dtstage = p_date;
    
    if v_idstage = 1 then p_stage_description:='Submitted and received';
    elsif v_idstage = 2 then p_stage_description := 'Confirmed, processed, sent to shipping';
    elsif v_idstage = 3 then p_stage_description := 'Shipped';
    elsif v_idstage = 4 then p_stage_description := 'Cancelled';
    elsif v_idstage = 5 then p_stage_description := 'Back-ordered';
    else p_stage_description := 'there is no status available';
    end if;

exception
    when others then
        p_stage_description:='THERE IS NO STATUS AVAILABLE';
        p_date := SYSDATE;

END;

--TESTING Q2
Declare
cursor test_basketids is
    select 4 as basketid from dual union all
    select 6 as basketid from dual;

lv_description varchar2(50);
lv_date bb_basketstatus.dtstage%type;
begin
    
    for eachrow in test_basketids loop
        status_sp(eachrow.basketid, lv_description, lv_date);
        DBMS_OUTPUT.PUT_LINE('The recent status of ' || eachrow.basketid|| ' is ' || lv_description || ' and was updated on ' || lv_date);
    end loop;
    
end;

/*
Q3: Assignment : Identifying Customers Brewbean’s wants to offer an incentive of free shipping to customers who haven’t returned to the site 
since a specified date. Create a procedure named PROMO_SHIP_SP that determines who these customers are and then updates the BB_PROMOLIST table 
accordingly. The procedure uses the following information:
• Date cutoff—Any customers who haven’t shopped on the site since this date should be included as incentive participants. 
Use the basket creation date to reflect shopper activity dates. 
• Month—A three-character month (such as APR) should be added to the promotion table to indicate which month free shipping is effective. 
• Year—A four-digit year indicates the year the promotion is effective. 
• promo_flag—1 represents free shipping. The BB_PROMOLIST table also has a USED column, which contains the default value N and is updated to 
Y when the shopper uses the promotion. 
Test the procedure with the cutoff date 15-FEB-12. Assign free shipping for the month APR and the year 2012.
*/

create or replace procedure PROMO_SHIP_SP(p_date bb_basket.dtcreated%type, p_month varchar2, p_year varchar2) as

cursor cur_shoppers is
    SELECT idshopper
    FROM bb_basket
    GROUP BY idshopper
    HAVING MAX(dtcreated) <= p_date;

begin

    for shopper in cur_shoppers loop
        insert into bb_promolist (idshopper, month, year, promo_flag) values (shopper.idshopper, p_month, p_year, 1);
    end loop;

end;

--TESTING Q3
declare
lv_date bb_basket.dtcreated%type := TO_DATE('15-FEB-12','DD-MON-YY');
lv_month varchar2(3) := 'APR';
lv_year varchar2(4) := '2012';

cursor cur_promolist is
    select * from bb_promolist;

begin
    promo_ship_sp(lv_date, lv_month,lv_year);
    
    DBMS_OUTPUT.PUT_LINE(' IDSHOPPER || MONTH || YEAR || PROMO_FLAG || USED');
    for rowpromolist in cur_promolist loop
        DBMS_OUTPUT.PUT_LINE(rowpromolist.idshopper ||'           '|| 
                            rowpromolist.month ||'      '|| 
                            rowpromolist.year ||'        '||
                            rowpromolist.promo_flag ||'         '||
                            rowpromolist.used);
    end loop;
    
end;




/*
Q4: Assignment : Using Packaged Variables In this assignment, you create a package that uses packaged variables to assist in the user 
logon process. When a returning shopper logs on, the username and password entered need to be verified against the database. 
In addition, two values need to be stored in packaged variables for reference during the user session: the shopper ID and 
the first three digits of the shopper’s zip code (used for regional advertisements displayed on the site).
1. Create a function that accepts a username and password as arguments and verifies these values against the database for a match. 
    If a match is found, return the value Y. Set the value of the variable holding the return value to N. 
    Include a NO_DATA_FOUND exception handler to display a message that the logon values are invalid. 
2. Use an anonymous block to test the procedure, using the username gma1 and the password goofy. 
3. Now place the function in a package, and add code to create and populate the packaged variables specified earlier. 
    Name the package LOGIN_PKG. 
4. Use an anonymous block to test the packaged procedure, using the username gma1 and the password goofy to verify that the procedure 
    works correctly. 
5. Use DBMS_OUTPUT statements in an anonymous block to display the values stored in the packaged variables.
*/

create or replace package LOGIN_PKG is
    gbl_shopperid bb_shopper.idshopper%type;
    gbl_shopper_zipcode varchar2(3);

    function validate_user_psw(p_user varchar2, p_password varchar2) return char;

end LOGIN_PKG;

create or replace package body LOGIN_PKG is


    function validate_user_psw(p_user varchar2, p_password varchar2) return char is
        lv_password bb_shopper.password%type;
        lv_idshopper bb_shopper.idshopper%type;
        lv_zipcode bb_shopper.zipcode%type;
    begin
        select idshopper, zipcode, password into lv_idshopper, lv_zipcode, lv_password from bb_shopper where username = p_user;
        
        gbl_shopperid := lv_idshopper;
        gbl_shopper_zipcode := SUBSTR(lv_zipcode,1,3);
        
        if (lv_password = p_password) then return 'Y';
        else return 'N';
        end if;
    
        exception
        when NO_DATA_FOUND then
        
            DBMS_OUTPUT.PUT_LINE('Logon values are invalid!');
            gbl_shopperid := 0;
            gbl_shopper_zipcode := 0;
            Return 'E';
        

    end validate_user_psw;


end LOGIN_PKG;

--TESTING Q4
declare
lv_user varchar2(10):= 'gma1';
lv_psw varchar2(10) := 'goofy';
lv_login_validation char;
begin
    lv_login_validation := login_pkg.validate_user_psw(lv_user,lv_psw);
    
    DBMS_OUTPUT.PUT_LINE('Was the login successful? ' || lv_login_validation);
    DBMS_OUTPUT.PUT_LINE('The shopper Id is:: ' || login_pkg.gbl_shopperid);
    DBMS_OUTPUT.PUT_LINE('The first three characters of zipcode are::' || login_pkg.gbl_shopper_zipcode);
    
end;

/*
Q5: Assignment : Using a Cursor in a Package In this assignment, you work with the sales tax computation because the Brewbean’s lead programmer
expects the rates and states applying the tax to undergo some changes. The tax rates are currently stored in packaged variables but need to 
be more dynamic to handle the expected changes. The lead programmer has asked you to develop a package that holds the tax rates by state 
in a packaged cursor. The BB_TAX table is updated as needed to reflect which states are applying sales tax and at what rates. 
This package should contain a function that can receive a two-character state abbreviation (the shopper’s state) as an argument, 
and it must be able to find a match in the cursor and return the correct tax rate. Use an anonymous block to test the function with 
the state value NC.
*/


create or replace package TAX_COMPUTATION_PKG is
    cursor cur_taxesbystates is
        select state, taxrate from bb_tax;

    function get_tax_rates(p_state varchar2) return bb_tax.taxrate%type;
    
end TAX_COMPUTATION_PKG;

create or replace package body TAX_COMPUTATION_PKG is
    
    function get_tax_rates(p_state varchar2) return bb_tax.taxrate%type is
        
    begin
        for taxbystate in cur_taxesbystates loop
            if taxbystate.state = p_state then return taxbystate.taxrate;
            end if;
        end loop;
        return 0;
    end get_tax_rates;


end TAX_COMPUTATION_PKG;

--TESTING Q5
declare
lv_tax bb_tax.taxrate%type;
lv_state bb_tax.state%type:='NC';
begin

    lv_tax := TAX_COMPUTATION_PKG.get_tax_rates(lv_state);
    DBMS_OUTPUT.PUT_LINE('The tax for the ' || lv_state || ' state is:: ' || lv_tax); 

end;


