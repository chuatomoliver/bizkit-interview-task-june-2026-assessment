PROBLEM 1:

DATES ONLY

Existing Data: Jan 10-15 2026

Overlap Validation Success, Test Data:
-Jan 11-15 2026
-Jan 12-17 2026

Overlap Validation Fail, Test Data: 
-Jan 8-12 2026
-Jan 7-16 2026

Issue:
The logic only checks if the new reservation’s start date falls inside the existing reservation range.

Fix (app.py):
   OLD CODE return start_b <= start_a <= end_b                 
   FIXED CODE return start_a <= end_b and start_b <= end_a      

 Validation update, to check both reservation ranges against each other, all overlapping cases are now detected, including partial overlaps and full containment.




PROBLEM 2:

Test Case: 

Step 1 - User selects starting date e.g. Jul 1, 2026
Step 2 - User selects end date e.g. Jul 15, 2026
Output 1 - Total: PHP 0.00 
Step 3 - User creates booking

-The event listener works properly after clicking the 'Create Booking'. Now, data is Jan 1, 2026 - Jul 15, 2026.

Step 4 - User selects new starting date, e.g. Jan 1, 2026
Output 2 - Total: PHP 294000.00 (196 days)
Step 5 - User refreshes the page
Step 6 - User selects starting date e.g. Jul 1, 2026
Step 7 - User selects end date e.g. Jul 15, 2026
Output 3 - Total: PHP 0.00 

-The event listener doesn't work on initial page load.




Issue:

Frontend: The end date input field has no event listener. Only the start date and equipment dropdowns trigger updateTotal(). When user sets the end date, updateTotal() never runs, so total stays at PHP 0.00 until they finally enter the initial data since the page load. When refreshed, the same problem shows.

Backend: The rental_days() function calculates days as (to_date - from_date).days which gives 4 for Jan 10-15. But rentals bill inclusively (both start and end day count), so Jan 10-15 should be 6 days, not 5.

Fix:

Frontend (index.html):
   OLD CODE:
   document.getElementById("equipment").addEventListener("change", updateTotal);
   document.getElementById("from").addEventListener("change", updateTotal);
   document.getElementById("book").addEventListener("click", createBooking);
   
   FIXED CODE:
   document.getElementById("equipment").addEventListener("change", updateTotal);
   document.getElementById("from").addEventListener("change", updateTotal);
   document.getElementById("to").addEventListener("change", updateTotal);             #This is the addition, new listener.
   document.getElementById("book").addEventListener("click", createBooking);

The new event listener to the end date field so that updateTotal() is called whenever the end date changes, it allows the total to display correctly as dates are selected.

Backend (app.py):
    OLD CODE return (to_date - from_date).days
    FIXED CODE return (to_date - from_date).days + 1 

Adding +1 to the day calculation ensures rentals are billed inclusively (both start and end dates count as billable days). 






