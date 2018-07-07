After receiving several questions about the previous post about the TFS Aggregator ("Meeting the TFS Aggregator") I decided to go deeper in this topic with a new post.
The idea is to expose some common use cases and their implementation for them that are getting started.
Before reading the post I highly recommend reading the <a href="https:/tfsaggregator.github.io/using/">official documentation</a> to understand the syntax basics.

<strong>Let's implement the following use cases:</strong>

<ul>
 	<li>Update PBI state to "Committed" when any child gets moved to "In Progress"</li>
	<li>Update PBI state to "Done" when all childrens get moved to "Done" or "Removed"</li>
	<li>Set a "calculated" field in a Task</li>
	<li>Create new work items and links</li>
	<li>Update a PBI field according to the value of the children</li>
	<li>Update children field values according to the parent changes (when the parent changes)</li>
	<li>Aggregate the field "Remaining Work" in a PBI with the children "Remaining Work" values</li>
</ul>


&nbsp;
&nbsp;

<strong>Update PBI state to "Committed" when any child gets moved to "In Progress"</strong>
When any task is moved to "In Progress" move it's parent to "Committed".

[code language="xml" gutter="true"]
<rule name="AutoCommittedByChildren" appliesTo="Task" >
	<![CDATA[
		if (new[] {"In Progress"}.Contains((string)self["System.State"]))
		{
			if(self.HasParent() && ((string)self.Parent["System.State"]) != "Committed")
			{
				self.Parent.TransitionToState("Committed", "Updated Automatically By TFS Aggregator");
			}
		}      
	]]>
</rule>	
[/code]


&nbsp;

<strong>Update PBI state to "Done" when all childrens get moved to "Done" or "Removed"</strong>

[code language="xml" gutter="true"]
<rule name="AutoDoneByChildren" appliesTo="Task">
    <![CDATA[
		if ((string)self["System.State"] == "Done" && self.HasParent() && ((string)self.Parent["System.State"]) != "Done")
		{
			if (self.Parent.Children.All(child => new[] {"Removed", "Done"}.Contains((string)child["System.State"])))
			{
				self.Parent.TransitionToState("Done", "Updated Automatically By TFS Aggregator");
			}
		}
    ]]>
</rule>	
[/code]


&nbsp;

<strong>Set a "calculated" field in a Task</strong>
Set the field "Estimated Work" with the sum of "Estimated Dev Work" and "Estimated Test Work".

[code language="xml" gutter="true"]
<rule name="CalculatedEstimatedWorkField" appliesTo="Task" >
	<![CDATA[
		self["Custom.EstimatedWork"] = (double)self["Custom.EstimatedDevWork"] + (double)self["Custom.EstimatedTestWork"];
	]]>
</rule>
[/code]


&nbsp;

<strong>Create new work items and links</strong>
When a PBI is opened, create a linked task with the same title, iteration path and area path as child.

[code language="xml" gutter="true"]
<rule name="CreateAutomaticTasksForPBI" appliesTo="Product Backlog Item" >
	<![CDATA[
		if ((string)self["System.State"] == "New" && ((int)self["System.Rev"]) == 1) 
		{
			if(!self.HasChildren()) 
			{
				var child = store.MakeNewWorkItem((string)self["System.TeamProject"], "Task");
				child["Title"] = self["Title"];
				child["System.IterationPath"] = self["System.IterationPath"];
				child["System.AreaPath"] = self["System.AreaPath"];
				child.AddWorkItemLink(self, WorkItemImplementationBase.ParentRelationship);
			}
		}
	]]>
</rule>
[/code]


&nbsp;

<strong>Update a PBI field according to the value of the children</strong>
Set the field "Estimated Work" in the PBI with it children "Estimated Work" sum.

[code language="xml" gutter="true"]
<rule name="CalculateByChlidrenEstimatedWork" appliesTo="Task">
	<![CDATA[
		if (self.HasParent())
		{
			var parent = self.Parent;
			parent["Custom.EstimatedWork"] = parent.Children.Sum(task => task.GetField<double>("Custom.EstimatedWork", 0d));
		}
	]]>
</rule>
[/code]


&nbsp;

<strong>Update children field values according to the parent values (when the parent changes)</strong>
Set "Iteration Path" and "Area Path" fields according to the parent values when the Area/Iteration path is updated in the PBI.

[code language="xml" gutter="true"]
<rule name="UpdateAreaAndIterationByParentChanges" appliesTo="Product Backlog Item" >
	<![CDATA[
		if(self.HasChildren()) 
		{
			foreach (var child in self.Children) 
			{
				if (((string)self["System.IterationPath"]) != ((string)self.LastRevision.Fields["System.IterationPath"].OriginalValue))
				{
					child["System.IterationPath"] = self["System.IterationPath"];
				}				
				if (((string)self["System.AreaPath"]) != ((string)self.LastRevision.Fields["System.AreaPath"].OriginalValue))
				{
					child["System.AreaPath"] = self["System.AreaPath"];
				}
			}					
		}      
	]]>
</rule>	
[/code]

&nbsp;

<strong>Aggregate the field "Remaining Work" in a PBI with the children "Remaining Work" values</strong>
When Task is updated aggregate the parent "Remaining Work" value (upate the total remaining work by calculating [total - delta]).
For this rule we will create a hidden field called "PreviousRemainingWork" in the Task to calculate the delta.

[code language="xml" gutter="true"]
<rule name="AggregateParentRemainingWork" appliesTo="Task" >
	<![CDATA[
		double delta = (double)(self["Custom.PreviousRemainingWork"]??0d) - (double)(self["Microsoft.VSTS.Scheduling.RemainingWork"]??0d);
		self.Parent["Microsoft.VSTS.Scheduling.RemainingWork"] = (double)(self.Parent["Microsoft.VSTS.Scheduling.RemainingWork"]??0d) + delta;
		self["Custom.PreviousRemainingWork"] = (double)(self["Microsoft.VSTS.Scheduling.RemainingWork"]??0d);
	]]>
</rule>	
[/code]


<strong>Putting all Together</strong>
Create a policies file (TFSAggregator.ServerPlugin.policies) with the content below:

[code language="xml" gutter="true"]
<?xml version="1.0" encoding="utf-8"?>
<AggregatorConfiguration>
    
	<!-- Configuration -->
    <runtime debug="false">
        <rateLimiting interval="00:00:01.00" changes="1" />
        <logging level="Normal" />
        <script language="C#" />
        <authentication autoImpersonate="false" />
    </runtime>
   
	<!--Rules-FirstPolicy-->	
    <rule name="AutoCommittedByChildren" appliesTo="Task" >
		<![CDATA[
			if (new[] {"In Progress"}.Contains((string)self["System.State"]))
			{
				if(self.HasParent() && ((string)self.Parent["System.State"]) != "Committed")
				{
					self.Parent.TransitionToState("Committed", "Updated Automatically By TFS Aggregator");
				}
			}      
		]]>
	</rule>	
	<rule name="AutoDoneByChildren" appliesTo="Task">
		<![CDATA[
			if ((string)self["System.State"] == "Done" && self.HasParent() && ((string)self.Parent["System.State"]) != "Done")
			{
				if (self.Parent.Children.All(child => new[] {"Removed", "Done"}.Contains((string)child["System.State"])))
				{
					self.Parent.TransitionToState("Done", "Updated Automatically By TFS Aggregator");
				}
			}
		]]>
	</rule>
	<rule name="CalculatedEstimatedWorkField" appliesTo="Task" >
		<![CDATA[
			self["Custom.EstimatedWork"] = (double)self["Custom.EstimatedDevWork"] + (double)self["Custom.EstimatedTestWork"];
		]]>
	</rule>
	
	<!--Rules-SecondPolicy-->	
	<rule name="CreateAutomaticTasksForPBI" appliesTo="Product Backlog Item" >
		<![CDATA[
			if ((string)self["System.State"] == "New" && ((int)self["System.Rev"]) == 1) 
			{
				if(!self.HasChildren()) 
				{
					var child = store.MakeNewWorkItem((string)self["System.TeamProject"], "Task");
					child["Title"] = self["Title"];
					child["System.IterationPath"] = self["System.IterationPath"];
					child["System.AreaPath"] = self["System.AreaPath"];
					child.AddWorkItemLink(self, WorkItemImplementationBase.ParentRelationship);
				}
			}
		]]>
	</rule>
	<rule name="CalculateByChlidrenEstimatedWork" appliesTo="Task">
		<![CDATA[
			if (self.HasParent())
			{
				var parent = self.Parent;
				parent["Custom.EstimatedWork"] = parent.Children.Sum(task => task.GetField<double>("Custom.EstimatedWork", 0d));
			}
		]]>
	</rule>
	<rule name="UpdateAreaAndIterationByParentChanges" appliesTo="Product Backlog Item" >
		<![CDATA[
			if(self.HasChildren()) 
			{
				foreach (var child in self.Children) 
				{
					if (((string)self["System.IterationPath"]) != ((string)self.LastRevision.Fields["System.IterationPath"].OriginalValue))
					{
						child["System.IterationPath"] = self["System.IterationPath"];
					}				
					if (((string)self["System.AreaPath"]) != ((string)self.LastRevision.Fields["System.AreaPath"].OriginalValue))
					{
						child["System.AreaPath"] = self["System.AreaPath"];
					}
				}					
			}      
		]]>
	</rule>	
	<rule name="AggregateParentRemainingWork" appliesTo="Task" >
		<![CDATA[
			double delta = (double)(self["Custom.PreviousRemainingWork"]??0d) - (double)(self["Microsoft.VSTS.Scheduling.RemainingWork"]??0d);
			self.Parent["Microsoft.VSTS.Scheduling.RemainingWork"] = (double)(self.Parent["Microsoft.VSTS.Scheduling.RemainingWork"]??0d) + delta;
			self["Custom.PreviousRemainingWork"] = (double)(self["Microsoft.VSTS.Scheduling.RemainingWork"]??0d);
		]]>
	</rule>	
	
	<!-- Policies -->
    <policy name="FirstPolicyName">
	
	    <!-- Scope -->
        <collectionScope collections="TfsCollectionName" />
        <projectScope projects="TfsTeamProjectName" />
        
        <!-- Rules -->
        <ruleRef name="AutoCommittedByChildren" />
		<ruleRef name="AutoDoneByChildren" />
		<ruleRef name="CalculatedEstimatedWorkField" />
        
    </policy>	
	<policy name="SecondPolicyName">

	    <!-- Scope -->
        <collectionScope collections="TfsCollectionName" />
        <projectScope projects="TfsTeamProjectName" />
        
        <!-- Rules -->
        <ruleRef name="CreateAutomaticTasksForPBI" />
		<ruleRef name="CalculateByChlidrenEstimatedWork" />
		<ruleRef name="UpdateAreaAndIterationByParentChanges" />
		<ruleRef name="AggregateParentRemainingWork" />
        
    </policy>
    
</AggregatorConfiguration>
[/code]

&nbsp;
&nbsp;

<strong>Some Tips</strong>

<ul>
 	<li>Remember that fields can be null (avoid exceptions)</li>
	<li>Use the logger to trace your steps</li>
	<li>Avoid infinite loops (use the rateLimiting configuration)</li>
	<li>Use hidden fields for calculated rules that require aggregation</li>
	<li>Use reference names attribute instead of the field name</li>
	<li>Source control your changes (using Git, TFVC, etc)</li>
</ul>


&nbsp;
&nbsp;