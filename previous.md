# Activiti Cloud and Activiti 6.x and 5.x
Activiti Cloud uses at is core Activiti 7.x which is an evolution from Activiti 6.x. But as it was mention before, Activiti 7.x will bring some changes in how we recommend to do things. Most of these changes will be simplifications to make sure that the framework itself doesn’t promote practices that has been recognised to cause huge pains on real life implementations. All these changes and simplifications are done with cloud environments in mind. If you are using Activiti 6.x or 5.x and it is working fine for you, don’t worry Activiti 7.x will work for you. But if you are looking into microservices and cloud environments and you are suffering to adopt BPM tools and frameworks Activiti Cloud will solve most of those problems for you.
It is also important to note that Activiti Cloud is built on top of award wining frameworks that are widely used in the industry, for this reason, you don’t need to hire specialised developers to extend and improve Activiti Cloud Services.

Activiti 7 at is core will maintain the process engine, but related services such as:
- IDM (User / Groups / Memberships),
- Forms,
- History Service
- Job executor
- Timers
- Email/Notification Service
- etc.
will be deprecated and refactored out in favour of integrations with third party components already provided by most cloud infrastructures. All these refactorings have the only goal of making sure that the Process Engine bootstrap and footprint is minimal and doesn't overlap with functionalities and behavior which is expected to be provided by the infrastructure.

Activiti Cloud is being built by engineers coming from different BPM and industry backgrounds and we are aiming to solve problems that are not being solved by these other proprietary or open source projects.

The team behind Activiti Cloud is very committed to Open Source practices and we will adopt and integrate our services with other Open Source projects that we believe that our community will benefit from. We will also participate and collaborate with other Open Source projects that share our interests in an Open Collaboration fashion. If you are working in another Open Source project are you are interested to integrate with us or to work in collaboration, please get in touch.
