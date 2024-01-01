#### Security Controls mitigates vulnerabilities and risk to ensure the confidentiality, integrity,availability, nonrepudiation, and authentication of data

#### Security controls should be selected and deployed in a structured manner using a risk management framework

#### Security Controls are classified using a special publication by NIST called "NIST SP 800-53". Its not needed to know this document for the exam but its useful for daily work.

Some examples of Security Control Categories from this document are: 

* Access Controls
* Accountability
* Incident Response 
* Risk Management

#### There is also a different standard for Security Controls called ISO27001, but this is not free like NIST. Thats why many orgs like to use NIST instead.

#### Earlier versions of the NIST SP 800-53 used classes of controls (technical, operational and managerial)

<u>Technical (Logical) Controls:
</u>

A category of security control that is implemented as a system (hardware, software or firmware)

Some examples of Technical / Logical controls are:

* firewall
* antivirus software
* patches on the OS

<u>Operational Controls:
</u>

A category of security control that is implemented primarily by people rather than systems

Some examples:

* security guards
* train employees not to fall for phishing scams

<u>Managerial Controls:
</u>

A category of security control that provides oversight of the information system

Some examples:

* risk identification
* policies for using tools like vulnerability scans and remidiations

#### Newer version of NIST SP 800-53 do not use classes of controls anymore, but these are still used by the CySA+ exam objectives.

#### Other types of Security Controls are known as "Functional Types" or "Functional Categories"

There are 3 different ones:

* Preventative (eliminate or reduce the likelihood that an attack can succeed. Example is an ACL on a firewall)
* Detective (this may not prevent or deter access, but will identify and record any attempted or successful intrusion. An example is: logs. Or a security camera)
* Corrective (eliminate or reduce the impact of an intrusion event. An example could be a backup system that can be used to restore something)

#### No single security control is invulnerable, so the efficiency of a control is instead measured by how long it delays an attack.

Some more controls:

* Physical (acts against in-person intrustion attempts. Example: alarms, gateways, locks, guards)
* Deterrent (discourages intrusion attempts)
* Compensating (a type of security control that acts as a substitute for a principal control. Example: a smartcard with a pincode instead of a 15 character password.)
* Responsive (system that actively monitors for potential vulnerabilities or attacks, and then takes actions to mitigate them before they can cause damage. Examples: Firewall, IDS, IPS)

#### Important to realise is that these control types can OVERLAP. So a physical control can also be a deterrent control, etc.

