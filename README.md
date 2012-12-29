To implement maekawa's algorithm, I defined three queues. One is waiting queue which collects the requests from other 
site. The second is locking queue which collects the locks send from other sites. The third is the FailMsg queue which 
collects the Fail message sending from other sites.
Assume the site i request to enter CS, and it sends out requests to all the other sites that belong to the same quorum. 
I defined the following functions to handle the whole process:
OnRequest:
When one site received a request from site i. Check if it has locked to other site before. If it did not, send 
Lock(self) message to site i, and make itself to be locked for site i. Otherwise, put the request from site i onto 
its waiting queue. Then check if the request from site i came earlier than the request from the site that it has 
locked for. If it does, send Inquire(self) message to the site it locked for. Otherwise, send Fail message to site i.

OnFail:
When one site received fail message from site j. Put site j onto its FailMsg queue.

OnInquire:
When one site received Inquire message from site j. First check if its FailMsg queue is empty or not. If it is not, 
remove all locked messages it received in its locking queue. Otherwise, if it's not in CS and the number of the locked 
messages it received is equal to the number of site belong to the same quorum. Then send Release message to site j.

OnRelinquish:
When one site received Relinquish message from site j. It would pop out the earliest request from its waiting queue, 
and locked for the site which sent that request before. Also put the previous site it locked for along with its request 
onto the waiting queue.

OnLock:
When one site received fail message from site j. Put site j onto its locking queue.

OnRelease:
When one site received Release message from site j. First check if its waiting queue is empty or not. If it is not, pop 
the request which comes the earliest from the waiting queue, and make the site which sent that request to be the site it
locked for.

When the number of locked message in the locking queue of site i is equal to the number of sites that belong to the same
quorum, it would enter the CS.
When site i wants to exit the CS, it would send Release message to all the sites that belong to the same quorum. 
Then upon receiving this message, those sites would release their locks for site i and send lock message to other site.
