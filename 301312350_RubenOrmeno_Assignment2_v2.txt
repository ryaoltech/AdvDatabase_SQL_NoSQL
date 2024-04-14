--Q1. Assignment3-5: Using a While Loop Brewbean's wants to include a feature in its application that calculates
-- the total amount (quantity) of a specified item that can be purchased with a given amount of money.

DECLARE

lv_money_amount NUMBER := 100;
lv_product_id NUMBER :=4;
lv_price NUMBER;
lv_number_items NUMBER:=0;

BEGIN

SELECT price into lv_price from bb_product where idproduct = lv_product_id;


while lv_money_amount > lv_price loop
    lv_money_amount := lv_money_amount - lv_price;
    lv_number_items:= lv_number_items + 1; 

end loop;

DBMS_OUTPUT.PUT_LINE('The total items we can purchase is:' || lv_number_items);
DBMS_OUTPUT.PUT_LINE('The amount of money we have now is:' || lv_money_amount);

END;


--Q2. Assignment 3-6: Working with IF statements Brewbean's calculates shipping cost based on the quantity
-- of items in an order. Assume the quantity column in the bb_basket table contains the total number of items
-- A block is needed to check the quantity provided by an initialized variable and determine the shipping cost.

--Testing the block with idbasket = 5 and idbasket = 12
DECLARE
lv_idbasket NUMBER := 5;
lv_quantity NUMBER;
lv_shipping_cost NUMBER := 0;

BEGIN

for i in 1..2 loop

select quantity into lv_quantity from bb_basket where idbasket = lv_idbasket;

if lv_quantity <= 3 then
    lv_shipping_cost := 5;
elsif lv_quantity between 4 and 6 then
    lv_shipping_cost := 7.5;
elsif lv_quantity between 7 and 10 then
    lv_shipping_cost := 10;
else 
    lv_shipping_cost := 12;
end if;

DBMS_OUTPUT.PUT_LINE('For idbasket='||lv_idbasket||', the shipping cost is: ' || lv_shipping_cost);


lv_idbasket := 12;

end loop;

END;


--Q3. Using Scalar variables for Data Retrieval the Brewbean's application contains a page displaying order
-- order summary information, including idbasket, subtotal, shipping, tax, and total columns from bb_basket table
-- Create pl/sql block with scalar variables to retrieve this data and then display it on screen.

DECLARE
lv_id_basket bb_basket.idbasket%type;
lv_subtotal bb_basket.subtotal%type;
lv_shipping bb_basket.shipping%type;
lv_tax bb_basket.tax%type;
lv_total bb_basket.total%type;
lv_idbasket_test bb_basket.idbasket%type := 12;
new_line char(1) := CHR(10);
BEGIN

select idbasket, subtotal, shipping, tax, total into lv_id_basket, lv_subtotal, lv_shipping, lv_tax, lv_total
from bb_basket
where idbasket = lv_idbasket_test;


DBMS_OUTPUT.PUT_LINE('The order summary information is:'|| new_line
        || 'ID Basket: ' ||lv_id_basket || new_line
        || 'Subtotal: ' || lv_subtotal || new_line
        || 'Shipping cost: ' || lv_shipping || new_line
        || 'Tax: ' || lv_tax || new_line
        || 'Total: ' || lv_total || new_line
);

END;

--Q4. Assignment 3-8: Using a record variable for data retrieval the Brewbean's application contains a page
-- displaying order summary information, including idbasket, subtotal, shipping, tax, and total columns
-- from the bb_basket table. Create a pl/sql block with a record variable to retrieve this data and display it on screen.

DECLARE

type tbl_summary is record (
    cl_idbasket bb_basket.idbasket%type,
    cl_subtotal bb_basket.subtotal%type,
    cl_shipping bb_basket.shipping%type,
    cl_tax bb_basket.tax%type,
    cl_total bb_basket.total%type
);

lv_rc_summary tbl_summary;

lv_idbasket number := 12;
new_line char(1) := CHR(10);
BEGIN

select idbasket, subtotal, shipping, tax, total into lv_rc_summary
from bb_basket where idbasket = lv_idbasket;

DBMS_OUTPUT.PUT_LINE('The order summary information is:'|| new_line
        || 'ID Basket: ' ||lv_rc_summary.cl_idbasket || new_line
        || 'Subtotal: ' || lv_rc_summary.cl_subtotal || new_line
        || 'Shipping cost: ' || lv_rc_summary.cl_shipping || new_line
        || 'Tax: ' || lv_rc_summary.cl_tax || new_line
        || 'Total: ' || lv_rc_summary.cl_total || new_line
);

END;

--Q5. Case 3-2: Working with more movie rentals the more movie rental company is developing an application page
-- that displays the total number of times a specified movie has been rented and the associated rental
-- rating based on this count.

--Running the block with movie_id = 4 and movie_id = 25
DECLARE
lv_n_rental NUMBER;
lv_movie_title mm_movie.movie_title%type;
lv_rating VARCHAR(4);
lv_movie_id NUMBER := 4;
new_line CHAR(1) := CHR(10);
BEGIN

for i in 1..2 loop

select count(*) into lv_n_rental from mm_rental where movie_id = lv_movie_id;

select movie_title into lv_movie_title from mm_movie where movie_id = lv_movie_id;

if lv_n_rental <= 5 then
    lv_rating := 'Dump';
elsif lv_n_rental between 6 and 20 then
    lv_rating := 'Low';
elsif lv_n_rental between 21 and 35 then
    lv_rating := 'Mid';
else
    lv_rating := 'High';
end if;

DBMS_OUTPUT.PUT_LINE('The movie renting information is:'|| new_line
        || 'Movie Title: ' || lv_movie_title || new_line
        || 'Rental count: ' || lv_n_rental || new_line
        || 'Rental rating: ' || lv_rating || new_line

);

lv_movie_id := 25;

end loop;

exception

when NO_DATA_FOUND then

DBMS_OUTPUT.PUT_LINE('No data found for lv_movie_id: ' || lv_movie_id || ' - ' || SQLERRM);

when others then

DBMS_OUTPUT.PUT_LINE('Error found: ' || SQLCODE || ' - ' || SQLERRM);


END;

