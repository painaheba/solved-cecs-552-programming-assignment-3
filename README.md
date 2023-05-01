Download Link: https://assignmentchef.com/product/solved-cecs-552-programming-assignment-3
<br>
Discrete-Event Simulation of Ride Sharing







<h1>Ride Sharing in Squaresville with Lynx</h1>

The streets and avenues of the city of Squaresville consist of twenty streets (1st through 20th) that run east-west, along with twenty avenues (again, 1st through 20th) that run north-south. Each intersection between a street <em>s </em>and avenue <em>a </em>is denoted by the coordinates (<em>s,a</em>). Also, Near each intersection are designated pick-up and drop-off stations for a privately owned and city-sponsored ride-sharing business called Lynx. To use Lynx, a potential rider uses an app to reserve a ride by providing the following information: i) pick-up coordinates, ii) drop-off coordinates, iii) number of passengers (1-4), and iv) whether or not the party wants to carpool with other passengers. Currently, the system only handles reservations for immediate travel, and so the ideal pick-up time is equal to the time the reservation is established.

Lynx prides itself in reliability and quality, and guarantees that i) a party will be picked-up no later than 15 minutes after its reservation was made, and ii) whenever a carpooling party experiences an intermediate stop (i.e. pick-ups or drop-offs of fellow carpool passengers), the location of that stop is always closer to the party’s final destination than any of the previous intermediate stops, and the initial pick-up location. In other words, intermediate stops are, in some sense, ”on the way”. If either guarantee is not met, then the party rides for free.

Registered Lynx drivers can join the current drivers pool at any time. Drivers receive a variable-rate percentage of the fare paid by each party that the driver has transported. Moreover, the rate declines when the supply of drivers exceeds the rider demand, and increases when there is a perceived driver shortage. Note also that a driver is still paid (so long as he or she is not at fault) for transporting a party that traveled for free due to a breach in reliability or quality. Therefore, it is important for Lynx to have enough drivers at any time, but not too many to where drivers find themselves idle much of the time.

<h1>Lynx Needs Your Help</h1>

Lynx has hired you to answer the following question: assuming a fixed number of drivers over a given two-hour time period, what is the least number of drivers needed so that the probabiity <em>p </em>that “no more than 5% of passengers ride free” is at least 0<em>.</em>90? where 0<em>.</em>90 is the left endpoint of a 95% confidence interval for <em>p</em>. To answer this question they want you to design the best algorithm possible for assigning party reservations to each driver. Moreover, they want you to prove concept by simulating use of the algorithm over the two-hour time period. Note that a percentage of passengers is calculated, as opposed to a percentage of reservations.

<h1>System Modeling</h1>

<h2>Probability Distributions</h2>

Over a given two-hour period, reservations are randomly made at a rate of 2 reservations per minute, with the time between successive reservations following an exponential distribution. All reservations are assumed independent.

The following table shows the percentage of reservations that are for a given size, and the percentage of those reservations that request carpooling.

<table width="543">

 <tbody>

  <tr>

   <td width="151"><strong>Number in Party</strong></td>

   <td width="201"><strong>Percent of Reservations</strong></td>

   <td width="190"><strong>Percent that Carpools</strong></td>

  </tr>

  <tr>

   <td width="151">1</td>

   <td width="201">60%</td>

   <td width="190">50%</td>

  </tr>

  <tr>

   <td width="151">2</td>

   <td width="201">25%</td>

   <td width="190">65%</td>

  </tr>

  <tr>

   <td width="151">3</td>

   <td width="201">10%</td>

   <td width="190">45%</td>

  </tr>

  <tr>

   <td width="151">4</td>

   <td width="201">5%</td>

   <td width="190">30%</td>

  </tr>

 </tbody>

</table>

Furthermore, if (<em>S</em><sub>1</sub><em>,A</em><sub>1</sub>) denotes the pick-up coordinates of a reservation, while (<em>S</em><sub>2</sub><em>,A</em><sub>2</sub>) denotes the drop-off coordinates, then <em>S</em><sub>1</sub>, <em>S</em><sub>2</sub>, and <em>A</em><sub>1 </sub>are assumed independent and uniformly distributed over {1<em>,</em>2<em>,…,</em>20}. However, due to the time of day, 75% of all drop-off <em>A</em><sub>2 </sub>values uniformly lie in {4<em>,</em>8<em>,</em>12<em>,</em>16}, while the other 25% uniformly lie in {1<em>,</em>2<em>,…,</em>20}−{4<em>,</em>8<em>,</em>12<em>,</em>16}. This is because most government and retail buildings are located at the multiple-of-four avenues.

For any pair of adjacent intersections, the time needed to travel from one to the other is normally distributed with a mean of one minute, and a standard deviation of 20 seconds. Note that this time includes time waiting at stop lights and stopping for passengers.

The passenger capacity of a vehicle is determined at random before the start of simulation by using the following table

<table width="335">

 <tbody>

  <tr>

   <td width="231"><strong>Vehicle Passenger Capacity</strong></td>

   <td width="104"><strong>Probability</strong></td>

  </tr>

  <tr>

   <td width="231">1</td>

   <td width="104">0.05</td>

  </tr>

  <tr>

   <td width="231">2</td>

   <td width="104">0.05</td>

  </tr>

  <tr>

   <td width="231">3</td>

   <td width="104">0.40</td>

  </tr>

  <tr>

   <td width="231">4</td>

   <td width="104">0.30</td>

  </tr>

  <tr>

   <td width="231">5</td>

   <td width="104">0.15</td>

  </tr>

  <tr>

   <td width="231">6</td>

   <td width="104">0.5</td>

  </tr>

 </tbody>

</table>

Note that vehicles are independently and randomly assigned a capacity based on the abofe distribution.

<h2>Events</h2>

The following is a list of the events that change the system state.

<strong>Reservation </strong>A party has made a reservation.

<strong>Reservation Assignment </strong>A driver has been assigned a reservation.

<strong>Intersection Arrival </strong>A driver has arrived at an intersection.

<strong>Pick Up </strong>A driver has picked-up a reservation.

<strong>Drop Off </strong>A driver has dropped off the party associated with a reservation.

<strong>Idle Arrival </strong>A driver with no assigned reservations has arrived at the intersection where she is to wait for her next assigned reservation.

<h2>System Initialization</h2>

<ol>

 <li>The system clock is initialized to <em>T </em>= 0.</li>

 <li>The future event list is initialized with all Reservation events that will occur over the two-hour period.</li>

 <li>Each driver is randomly placed at an intersection, where she is parked until receiving her first assigned reservation.</li>

 <li>Drivers initially have no assigned reservations.</li>

</ol>

<h1>Program Options</h1>

<ol>

 <li>Allow the user to input i) a vehicle capacity and location ii) pick-up and drop off locations for a reservation, iii) reservation party size, and iv) a list of locations of potential intermediate stops. The program then simulates driving to the pick-up location, then driving to the dropoff location, while making as many intermediate stops as possible without sacrificing quality. The program should print a list of intersections visited, along with the elapsed time (seconds rounded to the nearest integer) to travel between each adjacent pair of intersections.</li>

</ol>