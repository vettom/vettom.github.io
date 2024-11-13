# SRE-Error-budget

![SRE](https://vettom-images.s3.eu-west-1.amazonaws.com/generic/sre.jpg){: style="height:100px;width:150px" align=right }
1. Is error budget realistic.
2. What are the single source of burning off Error budget
3. Any low hanging fruits that can increase availability

Identify Hazards and calculate potential risks due the hazard. Eg: Loadbalance over load.
Categorize risks based on classes of risks
Prioritize risks based on their potential impact

> Risk = Probability * impact

#### Downtime is total of
* Time to detection
* Time to resolution
* Percentage of users impacted

## Documenting SLO
> SLO defines threshold/line which defines reliable/unreliable service
 Must include
 * Why the threshold is where it is
 * Why the SLI are appropriate for measuring the SLO
 * Identify monitoring data deliberately **excluded** from the SLIs.

 Track SLO version and evaluate it periodically


## Error Budget Policy


   An error budget policy describes how your business decides to trade off reliability work against other feature work when this SLO indicates a service is not reliable enough. The purpose of such a policy is to guide your organization into meaningful and appropriate action when the reliability of your service is threatened. 
  Here are seven considerations. 
  An effective error budget policy should result in engineering to improve reliability if the error budget is exhausted or threatened. If your error budget has been exhausted or is on the verge of being exhausted, a good error budget policy should empower your dev and SRE teams to re-prioritize work and features that improve service reliability. Your error budget policy should be clear about when it takes effect. This could be when you've exhausted your trailing four week budget or when your error budget is at risk. Maybe you've burnt through 50 percent of your monthly error budget in one week. The key here is to make it clear at what point the error budget policy takes effect, and it's totally fine and reasonable to have multiple trigger points with different actions depending on severity. You also want to describe how this happens. An effective error budget policy details the ins and outs of how your dev and SRE teams will prioritize reliability features. Often, this will be tied to the severity of the error budget miss. For example, if the error budget is threatened but not exhausted, your policy might say that the team will commit one developer to fixing all high priority issues from the resulting post mortem. At the other end of the spectrum, if you had sustained error budget exhaustion, say a period of months or even quarters, your policy may say the entire dev and SRE teams will work solely on reliability features until your error budget is sufficiently replenished. Your policy should also include consequences if this reliability work is just not happening. To give the policy real teeth, there have to be meaningful consequences for consistently failing to meet an SL0 over longer time horizons. Development team that's focused solely on features at the expense of reliability is not doing the best thing for their users. At Google for example, SRE teams can give the pager back to the dev team if reliability isn't being effectively prioritized. To provide an example of consequences, Google has an emergency protocol for prioritizing fixes for production issues called code yellow. Any engineer can request a code yellow be declared, and with executive agreement, a code yellow team will focus all of its effort on mitigation and resolution of the problem. Code yellows are really disruptive events but they're effective if organizational boundaries and bad incentives are getting in the way of fixing major production issues. Back to the principles, you want to make sure your policy is consistently applied. Carving out one special case for a team who really needs this feature to launch opens the doors to others demanding the same, and undermines the ability of the SRE teams to provide reliable service. Sometimes business priorities will dictate that this happens. For example, when a failure to push some feature to production would cause a breach of contract. Remember back where we discussed having a small number of silver bullets, where once or twice per year, the development teams can continue to push features even when the service is out of SL0. A silver bullet should always be followed by postmortem specifically focused on why it was necessary to ensure those circumstances are avoided in the future. Now, your engineers and product managers are only human. It's almost inevitable that disagreements between teams will crop up. So the policy should also be explicit about who the decision maker is in those circumstances. Executives should back the policy when escalated to and enforce its consequences. Make sure the policy names the decision maker for these situations. 
     Lastly, your policy should be agreed upon and signed off by all parties. Ultimately, the most important part of the policy is not its contents, but that all parties, product managers, developers. SREs, and executives, know about it and have agreed to abide by it. It's difficult to get people to adhere to a policy they never agreed to.

### Policy Thresholds
* Threshold 1 Automated alerts notify SRE of an At-risk SLO
* Threshold 2. SRE Conclude they need help to defend SLO and escalate to Devs
* Threshold 3. The 30day error budget is exhausted and the root cause has not been found; **Feature releases blocked**, dev team dedicated more resources.
* Threshold 4. The 90 day error budget is exhausted and the root cause has not been found. SRE ** escalates to executive leadership ** to obtain more engineering time for reliability work.