You don't need a fancy system to carry out the essence of chaos testing. You leveraged the basics of Kubernetes to implement a core Pod destructure using just a Python script in a small custom container then run as a CronJob.

With these steps you have learned:

- &#x2714; Install a container with a simple script that accesses the Kubernetes API
- &#x2714; Elevate the security context of a Pod to grant it access to the Kubernetes API.
- &#x2714; Write the application in a Cron Job so it randomly melts away Pods.
- &#x2714; Use annotations to affect behaviors.

> In the last year we've seen Chaos Engineering move from a much talked-about idea to an accepted, mainstream approach to improving and assuring distributed system resilience. As organizations large and small begin to implement Chaos Engineering as an operational process, we're learning how to apply these techniques safely at scale. The approach is definitely not for everyone, and to be effective and safe, it requires organizational support at scale. -- [ThoughtWorks Radar](https://www.thoughtworks.com/radar/techniques/chaos-engineering)

## References ##

- [Principles of Chaos Engineering](http://principlesofchaos.org/)
- [Fallacies of Distributed Computing Explained (PDF)](http://www.rgoarchitects.com/Files/fallacies.pdf)

------
<p style="text-align: center; padding: 1em; margin: 3em; margin-left: 10em; margin-right: 10em; border-; 1px; border-color: olive;  border-radius: 12px; border-style:outset">
<img align="left" src="./assets/jonathan-johnson.jpg" width="100" style="border-radius: 12px">
For a deeper understanding of these topics and more join <br>[Jonathan Johnson](http://www.dijure.com)<br> at various conferences, symposiums, workshops, and meetups.
<br><br>
<b>Software Architectures ★ Speaker ★ Workshop Hosting ★ Kubernetes & Java Specialist</b>
</p>