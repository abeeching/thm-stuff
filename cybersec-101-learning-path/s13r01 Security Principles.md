# Security Principles

## [Task 1] Introduction

When discussing security, we first need to understand our adversary. Our approaches to keeping systems secure may differ depending on the situation. If we're protecting a laptop from industrial espionage, then we may employ different mechanisms than we would if we were trying to stop a child from accessing a work computer. Understanding what we're trying to defend against is key. With that said, there are some fundamental concepts that may apply in a variety of cybersecurity situations, and so our goal in this room is to discuss these fundamentals.

## [Task 2] CIA

First, we'll have to define the components of security - how would we assess a system's security? The most common way is to think in terms of the _CIA triad_ - CIA being an acronym for "confidentiality, integrity, availability" and not the governmental organization.
- Confidentiality: Only the intended person(s) can access data.
- Integrity: The data cannot be (unintentionally or maliciously) altered, and if such alteration occurs, we should be able to detect it.
- Availability: The system or service must be available when needed.

How you implement the CIA triad will differ depending on the situation.
- For online shopping, you might want to know that your credit card is kept sufficiently confidential and that your data won't be breached. You may also want to make sure that the retailer can maintain the integrity of your data, such as your shipping address - you wouldn't want your purchase to go to someone else! Lastly, you want to make sure that the online service is available so you can actually shop.
- For patient records and other systems, the staff might have a vested interest (read: legal requirement) to maintain the confidentiality of your medical records. Integrity of this data is also extremely important, since modifications can result in different medicine being prescribed, which can lead to life-threatening effects. You also want to make sure that the medical system is available so the staff can make an informed decision on what to treat you with.

In some cases, you may not need to treat the components of the CIA triad equally. A university announcement doesn't have to be confidential, but you'd prefer if its integrity was maintained (that is, that no one edited the announcement and that it came from the university itself). There are other cases where we can't feasibly pay equal amounts of attention to all three components; we may need to sacrifice some availability for some much-needed confidentiality, as an example.

There are other components of security that aren't mentioned in the CIA triad. Here's two of them:
- Authenticity: Meaning a message or document is not fraudulent or counterfeit, i.e., it came from the claimed source.
- Nonrepudiation: The original source of a message should not be able to deny that they are the source. This is critical for shopping, patient diagnoses, and banking, among other things.

These characteristics may apply to different extents in different situations. You might be a little annoyed but otherwise OK if you had to deliver a T-shirt, with cash on delivery, only to find out that the customer never placed the order. You'd like to know that the order is authentic, but you might be willing to accept the recipient saying that they never placed such an order. You'd likely be more upset if you had to ship off thousands of cars, only to be told the order is fake - in this case, you'd be concerned greatly with these two characteristics.

In 1998, Donn Parker proposed the Parkerian Hexad, which defines a set of six security elements. These are
1. Availability
2. Utility
3. Integrity
4. Authenticity
5. Confidentiality
6. Possession

We've covered four elements in this list already. The remaining two are:
- Utility: The information should be in a useful/usable format. If a user loses a decryption key for their laptop, then while they have the laptop on them, they can't really do much with it. That's a utility failure.
- Possession: Our information should be protected from unauthorized taking, copying, controlling. Failures in possession include having drives stolen or computers infected with ransomware.

The static site includes five questions that need to be answered in a minute to get the first flag of the room. Each question is a scenario in which you have to determine what aspect of the CIA triad was needed/missing. Effectively, any time we need to verify something about a person or a document, that'll involve integrity. Any time we need to keep something secret, that involves confidentiality. Availability simply involves making sure a resource is available to people.

**[Task 2, Question 1] Click on "View Site" and answer the five questions. What is the flag that you obtained at the end?** - `THM{CIA_TRIAD}`

## [Task 3] DAD

The *DAD triad* characterizes how a system can be attacked:
- Disclosure: The release of secret data. Opposite of Confidentiality
- Alteration: Changing of data. Opposite of Integrity
- Destruction (or Denial): Removal of data, or removal of access to data. Opposite of Availability

Some real-life scenarios include:
- Attackers stealign medical records and dumping them online -> disclosure
- Attackers modifying patient medical records -> alteration
- Attackers making database systems unavailable in a hospital -> denial

Protecting against these three things is extremely important in cybersecurity. Note that we can't _really_ protect against all of these to the fullest extent. Focusing too much on confidentiality and integrity will naturally result in a loss of availability (as a simple example, a password on a system may be used to protect confidentiality, but the system would become less available to users once it's in place). In cybersecurity, we need to prioritize the components of the CIA triad; failing that, we can aim for a good balance.

**[Task 3, Question 1] The attacker managed to gain access to customer records and dump them online. What is this attack?** - Disclosure

**[Task 3, Question 2] A group of attackers were able to locate both the main and backup power supply systems and switch them off. As a result, the whole network was shut down. What is this attack?** - Destruction/Denial

## [Task 4] Fundamental Concepts of Security Models

Now that we know what we need to protect and defend against, how can we go about developing a system that does this? We can do this with security models. There are three basic models of security.

The first is the Bell-LaPadula model. While it doesn't handle file sharing that well, it aims to achieve confidentiality with three rules:
- Simple Security Property, "no read up": A subject at a lower security level cannot read an object at a higher security level. This stops lower, unauthorized users from reading sensitive information at higher levels.
- Star Security Property, "no write down": A subject at a higher security level cannot write to an object at a lower security level. This can stop higher-level users from accidentally leaving sensitive information at lower levels.
- Discretionary-Security Property: An access matrix is used to allow read and write operations. This is effectively a table that defines subjects and what objects they can read and write. Of course, the matrix is to adhere to the first two properties.

The second is the Biba model. While it doesn't handle internal threats well, such as those from malicious insiders, it aims to achieve integrity with two main rules:
- Simple Integrity Property, "no read down": A higher-integrity subject should not be reading from a lower-integrity object.
- Star Integrity Property, "no write up": A lower-integrity subject should not be writing to a higher-integrity object.

The last is the Clark-Wilson model, which also aims to achieve integrity, but with a different set of concepts:
- Constrained Data Item (CDI): The data type whose integrity we want to preserve.
- Unconstrained Data Item (UDI): All other data types outside of CDIs.
- Transformation Procedures (TPs): Programmed operations, such as reading and writing, designed to maintain the integrity of CDIs.
- Integrity Verification Procedures (IVPs): Procedures to check and ensure the validity of CDIs.

Other models include
- Brewer-Nash
- Goguen-Meseguer
- Sutherland
- Graham-Denning
- Harrison-Ruzzo-Ullman

The next static site we have to deploy requires us to answer questions about the different models. We are effectively given a series of characteristics about a model, and we have to identify the model from a series of three choices. The information above can be used to answer the questions. Remember that in Bell-LaPadula, we don't read up nor write down (alternatively, we can read down and write up). In Biba, this is all flipped; we can't read down or write up, but we can read up and write down.

**[Task 4, Question 1] Click on "View Site" and answer the four questions. What is the flag that you obtained at the end?** - `THM{SECURITY_MODELS}`

## [Task 5] Defense-in-Depth

Defense-in-depth, also known as multi-level security, is the process of creating a security system with multiple levels. In a real-life context, this would be having many safeguards to prevent people from getting to your belongings. You might lock the drawer, then the room that it's in, then the front door of the house, then the gate, then you might even have some security cameras, and so on... While we cannot stop every attacker, defense-in-depth does a good job at blocking many of them and slowing down the rest.

## [Task 6] ISO/IEC 19249

The International Organization for Standardization (ISO) and International Electrotechnical Commission (IEC) created the ISO/IEC 19249 to define a series of principles for designing/developing secure products, systems, and apps. There's plenty other documents from folks globally on how to handle cybersecurity, but this one's a good place to start.

The five _architectural_ principles as defined in the document are:
1. Domain Separation - group all sets of related components as single entities. This can include apps, data, other resources. Each entity gets its own domain and is assigned a common set of security attributes.
2. Layering - Split a system into abstract levels and layers, and apply different security policies for each level.
3. Encapsulation - Hide low-level implementations and prevent direct manipulation of data in an object. Provide methods that manipulate data on behalf of the user instead.
4. Redundancy - Ensure the availability and integrity of systems by implementing redundancy mechanisms, such as RAID configurations.
5. Virtualization - Share a single set of hardware among multiple operating systems, and leverage sandboxing capabilities to deal with malware.

The five _design_ principles are as follows:
1. Least Privilege: Provide only the amount of permissions needed for a user to carry out their job and nothing more.
2. Attack Surface Minimization: Attempt to minimize vulnerabilities by removing ways an attacker could get in; for instance, disable services that you absolutely do not need.
3. Centralized Parameter Validation: Perform parameter validation to ensure that inputs are correct and won't cause a vulnerability. This should be handled by one library or system.
4. Centralized General Security Services: Centralize all security services while ensuring availability and preventing a single point of failure from arising.
5. Preparing for Error and Exception Handling: Take into account the fact that errors and exceptions will occur. Design systems to be fail-safe, and ensure that information isn't leaked (e.g., in errors) upon failure.

The questions below pertain to the five design principles.

**[Task 6, Question 1] Which principle are you applying when you turn off an insecure server that is not critical to the business?** - 2

**[Task 6, Question 2] Your company hired a new sales representative. Which principle are they applying when they tell you to give them access only to the company products and prices?** - 1

**[Task 6, Question 3] While reading the code of an ATM, you noticed a huge chunk of code to handle unexpected situations such as network disconnection and power failure. Which principle are they applying?** - 5

## [Task 7] Zero Trust versus Trust But Verify

Some inherent trust is needed in order for society to function. At the technological level, if you suspected that your system had spyware on it, you might go so far as to rebuild the entire system. If you didn't trust the hardware vendor, you simply wouldn't use the system. Folks have found ways to reconcile the need for trust with the need to keep our systems secure.

One way is to implement a Trust-but-Verify approach: Always verify, even when you trust an entity and its behavior (including users and systems). This requires propper logging mechanisms (or something similar) to check for abnormalities. You can't verify everything, of course; it would be better to have an automated security mechanism for this purpose. Some of these, such as proxies, IDS, and IPS are things we have discussed in this learning path.

Another way is to assume Zero Trust; that is, treat trust itself as a vulnerability. Treat all entities as potential adversaries unless proven otherwise. Do not grant trust to devices based on their location or ownership. While this conflicts with older approaches for trust (e.g, trusting internal networks and enterprise-owned devices; requiring authentication and authorization), this does help mitigate the damage caused by a breach. It'll be contained.

One implementation of Zero Trust would be _microsegmentation_: let a network segment be as small as a single host as needed, require authentication for communication between segments, perform access control list checks, and meet other security requirements.

There _is_ a limit to how much people can apply Zero Trust before a business gets negatively impacted. This does NOT mean that you shouldn't apply it as long as feasible.

## [Task 8] Threat versus Risk

Three more terms to note down before we conclude. People often get these mixed up:
- A _vulnerability_ is a weakness in a system that could lead it to attacks or damage. (Related is an _exploit_ - something that _leverages_ a vulnerability.)
- A _threat_ is a potential danger associated with the weakness or vulnerability.
- A _risk_ is the likelihood that a threat actor will exploit a vulnerability, and the consequent impact on business.

Say we're working with a database system to store medical records. We learn that the database system has a vulnerability, and not only that, but a proof-of-concept exploit code has been released. This code tells us the threat is real, so now we must determine what the risk to the organization is from there. This is all covered in different rooms.

## [Task 9] Conclusion

Those are the fundamentals of security - the CIA triad tells us what we want to maintain in our systems, and the DAD triad shows us what attacks we're trying to avoid. We also looked over various security models, documents regarding security, and terminology/concepts. There is one more thing to discuss - the Shared Responsibility Model. This is a cloud-specific concept; cloud security gets pretty tricky since users can have different access levels depending on the cloud services they use. Users who have infrastructure-as-a-service (IaaS) have complete control and responsibility for the security of the system, whereas a user who has software-as-a-service (SaaS) has no direct access to the underlying system and thus has no responsibility for its security. That is left to the cloud service providers.
