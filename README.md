This work represents an on-going project that's lasted more than a decade to solve the conflict serializability problem completely, especially in 
a distributed database system. Some applications like financial ones require serializability in an OLTP system since they don't tolerate inconsistency. 
I've recently performed a thorough survey in commercial database systems and found out that there are four serializability implementations: 
MySQL InnoDB's 2PL, PostgreSQL's Serializable Snapshot Isolation, Google's Spanner's isolation level(the proof is given by me in Appendix D, 
the google guys may not already know this) and CockroachDB's timestamp-based serializability implementation. Their characteristics and issues 
are discussed throughout this article. The theoretical framework in this article can not only explain these four implementations, but also gives rise
to one built on top of MySQL Cluster's Read-Committed isolation level.

The most challenging OLTP applications we have to deal with are those consistent, large(usually implies a distributed architecture) and 
performance-boosted ones. In the 80-20 rule, these applications definitely fall into the toughest 20% category. None of the four known 
implementations can provide a satisfactory solution: MySQL InnoDB's 2PL and PostgreSQL's Serializable Snapshot Isolation are standalone
implementations that can't solve a large problem; Google's Spanner's serializability implementation feeds Read-Write transactions to a 2PL 
component and hence can't sustain intense Read-Write loads due to the thrashing behavior of 2PL; CockroachDB's timestamp-based 
serializability implementation's issues, including a starvation problem, are explained in Appendix D.

MySQL Cluster can scale up to 144 data nodes now and it is an in-memory database, this makes the status of the last two requirements 
look pretty good. The 2nd-tier serializability implementation I've developed for MySQL Cluster will not only provide consistency, but also 
is optimized for performance with the so-called Generalized Serializability Theorems. When the abyss of developing a
consistent, large and performance-boosted database application stares at us, we may stare back with it for the first time. I truly hope it will 
demonstrate us that it satisfies all three requirements in the peer-review process I'll host here and at reddit. If it would, the scalability of MySQL 
Cluster also implies it could cover the low-end 80% of applications too and the conflict serializability problem could have been solved; If 
it wouldn't, I will provide two 3rd-tier implementations based on this same framework in subsequent work trying to to rescue. There is a 
chance we could solve it completely in this spin.

This work also attempts to address the thrashing behavior of 2PL. One of the approach I've taken is to resolve conflicts to field level so that 
minimum conflicts result in the analysis, which should be able to lead to minimum lock contention(it requires a field level capable locking 
system which I'll develop in subsequent work). Also, this framework can explain all five serializability implementations make it a candidate 
to replace the relevant part of the current SQL standard, which only allows 2PL. 

The content in the ConflictSerializability.html file represents the current status of the project and the subreddit I am hosting for the discussion 
is here: https://www.reddit.com/r/Serializability/.
Please join me in the discussion if you feel intrigued. You are also invited to try on the serializability implementation I've developed for
MySQL Cluster and give me some feedback. A free and a paid service for that purpose is included in ConflictSerializability.html.

