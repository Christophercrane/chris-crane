<div class="WordSection1"><span style="font-size:36.0pt">Playbook for Polyglot Bot</span> Christopher Crane Morgan <span class="SpellE">Langlais</span> <span style="font-size:12.0pt;font-family:Cambria;mso-ascii-theme-font:minor-latin;
mso-fareast-font-family:&quot;\FF2D\FF33 \660E\671D&quot;;mso-fareast-theme-font:minor-fareast;
mso-hansi-theme-font:minor-latin;mso-bidi-font-family:&quot;Times New Roman&quot;;
mso-bidi-theme-font:minor-bidi;mso-ansi-language:EN-US;mso-fareast-language:
EN-US;mso-bidi-language:AR-SA"></span>**Introduction and Overview** Polyglot Bot (Poly) enables skills to be consumed in a wide variety of languages by sending user utterances and responses to the Watson Language Translator service through a Cloud Function. This run book will walk through: <span style="mso-fareast-font-family:Cambria;mso-fareast-theme-font:minor-latin;
mso-bidi-font-family:Cambria;mso-bidi-theme-font:minor-latin"><span style="mso-list:Ignore"><span style="font:7.0pt &quot;Times New Roman&quot;"></span>i.<span style="font:7.0pt &quot;Times New Roman&quot;"></span></span></span> Gathering Requirements <span style="mso-fareast-font-family:Cambria;mso-fareast-theme-font:minor-latin;
mso-bidi-font-family:Cambria;mso-bidi-theme-font:minor-latin"><span style="mso-list:Ignore"><span style="font:7.0pt &quot;Times New Roman&quot;"></span>ii.<span style="font:7.0pt &quot;Times New Roman&quot;"></span></span></span> Assembling the components <span style="mso-fareast-font-family:Cambria;mso-fareast-theme-font:minor-latin;
mso-bidi-font-family:Cambria;mso-bidi-theme-font:minor-latin"><span style="mso-list:Ignore"><span style="font:7.0pt &quot;Times New Roman&quot;"></span>iii.<span style="font:7.0pt &quot;Times New Roman&quot;"></span></span></span> Configuring Watson Assistant <span style="mso-fareast-font-family:Cambria;mso-fareast-theme-font:minor-latin;
mso-bidi-font-family:Cambria;mso-bidi-theme-font:minor-latin"><span style="mso-list:Ignore"><span style="font:7.0pt &quot;Times New Roman&quot;"></span>iv.<span style="font:7.0pt &quot;Times New Roman&quot;"></span></span></span> Patterns and other considerations The intended user experience for Poly enabled Assistants should be seamless. The end user selects a language and is able to interact with the Assistant in the language of their choice. The example below shows a user asking for and receiving an address to an establishment in Spanish despite the fact that the Assistant was configured and trained only in English. <span style="mso-no-proof:yes">![](Playbook%20for%20Polyglot%20bot_files/image002.png)</span> The user experience is enabled with the following component flow:<span style="mso-no-proof:yes">![](Playbook%20for%20Polyglot%20bot_files/image004.png)</span> <span class="GramE">This flow is made possible by linking Watson Assistant</span> to a custom application that sits in [Cloud Functions.](https://cloud.ibm.com/functions/) The Cloud Function is invoked by a <span class="SpellE">webhook</span> from Watson Assistant. Once invoked, the Cloud Function leverages both Language Translator and Assistant to correctly respond to the user. **<span style="mso-fareast-font-family:Cambria;
mso-fareast-theme-font:minor-latin;mso-bidi-font-family:Cambria;mso-bidi-theme-font:
minor-latin"><span style="mso-list:Ignore">1.<span style="font:7.0pt &quot;Times New Roman&quot;"></span></span></span> ****Gathering Requirements** In your IBM Cloud account you should have the following <span style="font-family:Symbol;mso-fareast-font-family:Symbol;mso-bidi-font-family:
Symbol"><span style="mso-list:Ignore">·<span style="font:7.0pt &quot;Times New Roman&quot;"></span></span></span> <span style="mso-fareast-font-family:&quot;Times New Roman&quot;">Watson Assistant [instance](https://cloud.ibm.com/developer/watson/services).</span> <span style="font-family:Symbol;mso-fareast-font-family:Symbol;mso-bidi-font-family:
Symbol"><span style="mso-list:Ignore">·<span style="font:7.0pt &quot;Times New Roman&quot;"></span></span></span> <span style="mso-fareast-font-family:&quot;Times New Roman&quot;">Language Translator [instance](https://cloud.ibm.com/developer/watson/services).</span> <span style="font-family:Symbol;mso-fareast-font-family:Symbol;mso-bidi-font-family:
Symbol"><span style="mso-list:Ignore">·<span style="font:7.0pt &quot;Times New Roman&quot;"></span></span></span> <span style="mso-fareast-font-family:&quot;Times New Roman&quot;">Cloud Functions [instance](https://cloud.ibm.com/functions/)</span> On your local computer you should have the following <span style="font-family:Symbol;mso-fareast-font-family:Symbol;mso-bidi-font-family:
Symbol"><span style="mso-list:Ignore">·<span style="font:7.0pt &quot;Times New Roman&quot;"></span></span></span> <span style="mso-fareast-font-family:&quot;Times New Roman&quot;">[IBM Cloud CLI](https://cloud.ibm.com/docs/cli)</span> <span style="font-family:Symbol;mso-fareast-font-family:Symbol;mso-bidi-font-family:
Symbol"><span style="mso-list:Ignore">·<span style="font:7.0pt &quot;Times New Roman&quot;"></span></span></span> Download the files in the repository [here](https://github.com/Christophercrane/chris-crane/tree/master/polyglotbot) which includes the following: <span style="font-family:Wingdings;mso-fareast-font-family:Wingdings;mso-bidi-font-family:
Wingdings"><span style="mso-list:Ignore">§<span style="font:7.0pt &quot;Times New Roman&quot;"></span></span></span> <span class="GramE">polyglot.zip</span> (Cloud Function code and dependencies) <span style="font-family:Wingdings;mso-fareast-font-family:Wingdings;mso-bidi-font-family:
Wingdings"><span style="mso-list:Ignore">§<span style="font:7.0pt &quot;Times New Roman&quot;"></span></span></span> <span class="GramE">skill</span>-polyglot-<span class="SpellE">bot.json</span> (the Watson assistant skill) **<span style="mso-fareast-font-family:Cambria;
mso-fareast-theme-font:minor-latin;mso-bidi-font-family:Cambria;mso-bidi-theme-font:
minor-latin"><span style="mso-list:Ignore">2.<span style="font:7.0pt &quot;Times New Roman&quot;"></span></span></span> ****Create Cloud Function:** Cloud Functions is a <span class="SpellE">serverless</span> <span style="mso-fareast-font-family:&quot;Times New Roman&quot;;mso-bidi-font-family:&quot;Times New Roman&quot;">programming platform for developing code that <span class="SpellE">scalably</span> executes on demand. We will be leveraging this tool because it integrates with Watson Assistant via a <span class="SpellE">Webhook</span> and carries low costs.</span> <span style="mso-fareast-font-family:Cambria;mso-fareast-theme-font:minor-latin;
mso-bidi-font-family:Cambria;mso-bidi-theme-font:minor-latin"><span style="mso-list:Ignore"><span style="font:7.0pt &quot;Times New Roman&quot;"></span>i.<span style="font:7.0pt &quot;Times New Roman&quot;"></span></span></span> Create an instance of Cloud Functions if you haven’t already <span style="mso-fareast-font-family:Cambria;mso-fareast-theme-font:minor-latin;
mso-bidi-font-family:Cambria;mso-bidi-theme-font:minor-latin"><span style="mso-list:Ignore"><span style="font:7.0pt &quot;Times New Roman&quot;"></span>ii.<span style="font:7.0pt &quot;Times New Roman&quot;"></span></span></span> Create a Cloud Function and note the name <span style="mso-fareast-font-family:Cambria;mso-fareast-theme-font:minor-latin;
mso-bidi-font-family:Cambria;mso-bidi-theme-font:minor-latin"><span style="mso-list:Ignore"><span style="font:7.0pt &quot;Times New Roman&quot;"></span>iii.<span style="font:7.0pt &quot;Times New Roman&quot;"></span></span></span> Login to your IBM Cloud from the CLI using the following command: <span class="SpellE"><span class="GramE">**<span style="mso-bidi-font-family:
Arial;color:#FABF8F;mso-themecolor:accent6;mso-themetint:153;background:black;
mso-highlight:black">ibmcloud</span>**</span></span>** <span style="mso-bidi-font-family:Arial;color:#FABF8F;mso-themecolor:
accent6;mso-themetint:153;background:black;mso-highlight:black">login –<span class="SpellE">sso</span></span>**<span style="color:#FABF8F;mso-themecolor:
accent6;mso-themetint:153"></span> <span style="mso-fareast-font-family:Cambria;mso-fareast-theme-font:minor-latin;
mso-bidi-font-family:Cambria;mso-bidi-theme-font:minor-latin"><span style="mso-list:Ignore"><span style="font:7.0pt &quot;Times New Roman&quot;"></span>iv.<span style="font:7.0pt &quot;Times New Roman&quot;"></span></span></span> **<span style="mso-bidi-font-family:Arial;
color:black">Choose the target space:</span>** <span class="SpellE"><span class="GramE"><span style="mso-bidi-font-family:Arial;color:#FABF8F;mso-themecolor:
accent6;mso-themetint:153;background:black;mso-highlight:black">ibmcloud</span></span></span> <span style="mso-bidi-font-family:Arial;color:#FABF8F;mso-themecolor:accent6;
mso-themetint:153;background:black;mso-highlight:black">target --<span class="SpellE">cf</span></span> <span style="mso-fareast-font-family:Cambria;mso-fareast-theme-font:minor-latin;
mso-bidi-font-family:Cambria;mso-bidi-theme-font:minor-latin"><span style="mso-list:Ignore"><span style="font:7.0pt &quot;Times New Roman&quot;"></span>v.<span style="font:7.0pt &quot;Times New Roman&quot;"></span></span></span> Place your polyglot.zip file in its own folder on your computer where you can find it. <span style="mso-fareast-font-family:Cambria;mso-fareast-theme-font:minor-latin;
mso-bidi-font-family:Cambria;mso-bidi-theme-font:minor-latin"><span style="mso-list:Ignore"><span style="font:7.0pt &quot;Times New Roman&quot;"></span>vi.<span style="font:7.0pt &quot;Times New Roman&quot;"></span></span></span> Using IBM Cloud CLI Locate the folder containing your zip file (polyglot.zip). <span style="mso-fareast-font-family:Cambria;mso-fareast-theme-font:minor-latin;
mso-bidi-font-family:Cambria;mso-bidi-theme-font:minor-latin"><span style="mso-list:Ignore"><span style="font:7.0pt &quot;Times New Roman&quot;"></span>vii.<span style="font:7.0pt &quot;Times New Roman&quot;"></span></span></span> From within the folder, create your cloud function with the following command from the CLI: <span class="SpellE"><span class="GramE"><span style="mso-bidi-font-family:&quot;Times Roman&quot;;color:#FABF8F;
mso-themecolor:accent6;mso-themetint:153;background:black;mso-highlight:black">ibmcloud</span></span></span> <span style="mso-bidi-font-family:&quot;Times Roman&quot;;color:#FABF8F;mso-themecolor:accent6;
mso-themetint:153;background:black;mso-highlight:black"><span class="SpellE">fn</span> action create [your cloud function name here] polyglot.zip --kind nodejs:10</span><span style="color:#FABF8F;mso-themecolor:accent6;mso-themetint:153"></span> `<span style="mso-ansi-font-size:12.0pt;mso-bidi-font-size:12.0pt;font-family:Cambria;
mso-ascii-theme-font:minor-latin;mso-fareast-font-family:Cambria;mso-fareast-theme-font:
minor-latin;mso-hansi-theme-font:minor-latin;mso-bidi-font-family:Cambria;
mso-bidi-theme-font:minor-latin"><span style="mso-list:Ignore"><span style="font:7.0pt &quot;Times New Roman&quot;"></span> viii.<span style="font:7.0pt &quot;Times New Roman&quot;"></span></span></span> ``<span style="mso-ansi-font-size:
12.0pt;mso-bidi-font-size:12.0pt;font-family:Cambria;mso-ascii-theme-font:minor-latin;
mso-hansi-theme-font:minor-latin;mso-bidi-font-family:&quot;Times New Roman&quot;;
mso-bidi-theme-font:minor-bidi">Once the creation is complete you should get a confirmation node. You can always update this file later if necessary with the following command:</span>` <span class="SpellE"><span class="GramE"><span style="mso-bidi-font-family:&quot;Times Roman&quot;;color:#FABF8F;
mso-themecolor:accent6;mso-themetint:153;background:black;mso-highlight:black">ibmcloud</span></span></span> <span style="mso-bidi-font-family:&quot;Times Roman&quot;;color:#FABF8F;mso-themecolor:accent6;
mso-themetint:153;background:black;mso-highlight:black"><span class="SpellE">fn</span> action update [your cloud function name here] polyglot.zip --kind nodejs:10</span>`<span style="mso-ansi-font-size:12.0pt;mso-bidi-font-size:12.0pt;font-family:Cambria;
mso-ascii-theme-font:minor-latin;mso-hansi-theme-font:minor-latin;mso-bidi-font-family:
&quot;Times New Roman&quot;;mso-bidi-theme-font:minor-bidi;color:#FABF8F;mso-themecolor:
accent6;mso-themetint:153"></span>` `<span style="mso-ansi-font-size:12.0pt;mso-bidi-font-size:12.0pt;font-family:Cambria;
mso-ascii-theme-font:minor-latin;mso-fareast-font-family:Cambria;mso-fareast-theme-font:
minor-latin;mso-hansi-theme-font:minor-latin;mso-bidi-font-family:Cambria;
mso-bidi-theme-font:minor-latin"><span style="mso-list:Ignore"><span style="font:7.0pt &quot;Times New Roman&quot;"></span> ix.<span style="font:7.0pt &quot;Times New Roman&quot;"></span></span></span> ``<span style="mso-ansi-font-size:12.0pt;mso-bidi-font-size:12.0pt;font-family:Cambria;
mso-ascii-theme-font:minor-latin;mso-hansi-theme-font:minor-latin">Once you have logged into the IBM Cloud CLI navigate to the folder holding the downloaded zip file and enter the following:</span>``<span style="mso-ansi-font-size:12.0pt;mso-bidi-font-size:12.0pt;font-family:Cambria;
mso-ascii-theme-font:minor-latin;mso-hansi-theme-font:minor-latin;mso-bidi-font-family:
&quot;Times New Roman&quot;;mso-bidi-theme-font:minor-bidi"></span>` <span class="SpellE"><span class="GramE">`<span style="mso-ansi-font-size:12.0pt;mso-bidi-font-size:
12.0pt;font-family:Cambria;mso-ascii-theme-font:minor-latin;mso-hansi-theme-font:
minor-latin;color:#FABF8F;mso-themecolor:accent6;mso-themetint:153;background:
black;mso-highlight:black">ibmcloud</span>`</span></span>` <span style="mso-ansi-font-size:12.0pt;mso-bidi-font-size:12.0pt;font-family:Cambria;
mso-ascii-theme-font:minor-latin;mso-hansi-theme-font:minor-latin;color:#FABF8F;
mso-themecolor:accent6;mso-themetint:153;background:black;mso-highlight:black"><span class="SpellE">fn</span> action create my-action my-action.zip --kind nodejs:10</span>``<span style="mso-ansi-font-size:12.0pt;mso-bidi-font-size:
12.0pt;font-family:Cambria;mso-ascii-theme-font:minor-latin;mso-hansi-theme-font:
minor-latin;mso-bidi-font-family:&quot;Times New Roman&quot;;mso-bidi-theme-font:minor-bidi;
color:#FABF8F;mso-themecolor:accent6;mso-themetint:153"></span>` `<span style="mso-ansi-font-size:12.0pt;mso-bidi-font-size:12.0pt;font-family:Cambria;
mso-ascii-theme-font:minor-latin;mso-fareast-font-family:Cambria;mso-fareast-theme-font:
minor-latin;mso-hansi-theme-font:minor-latin;mso-bidi-font-family:Cambria;
mso-bidi-theme-font:minor-latin"><span style="mso-list:Ignore"><span style="font:7.0pt &quot;Times New Roman&quot;"></span> x.<span style="font:7.0pt &quot;Times New Roman&quot;"></span></span></span> ``<span style="mso-ansi-font-size:12.0pt;mso-bidi-font-size:12.0pt;font-family:Cambria;
mso-ascii-theme-font:minor-latin;mso-hansi-theme-font:minor-latin">This command will push the code to the cloud function and make it available for your skill.</span>``<span style="mso-ansi-font-size:12.0pt;mso-bidi-font-size:12.0pt;font-family:Cambria;
mso-ascii-theme-font:minor-latin;mso-hansi-theme-font:minor-latin;mso-bidi-font-family:
&quot;Times New Roman&quot;;mso-bidi-theme-font:minor-bidi"></span>` `<span style="mso-ansi-font-size:
12.0pt;mso-bidi-font-size:12.0pt;font-family:Cambria;mso-ascii-theme-font:minor-latin;
mso-hansi-theme-font:minor-latin;mso-bidi-font-family:&quot;Times New Roman&quot;;
mso-bidi-theme-font:minor-bidi"></span>`

<table class="MsoTableMediumShading1" style="width:477.0pt;margin-left:63.9pt;border-collapse:collapse;
 border:none;mso-border-alt:solid #404040 1.0pt;mso-border-themecolor:text1;
 mso-border-themetint:191;mso-yfti-tbllook:1184;mso-padding-alt:0in 5.4pt 0in 5.4pt" width="477" cellspacing="0" cellpadding="0" border="1">

<tbody>

<tr style="mso-yfti-irow:-1;mso-yfti-firstrow:yes">

<td style="width:189.0pt;border:solid #404040 1.0pt;
  mso-border-themecolor:text1;mso-border-themetint:191;border-right:none;
  background:black;mso-background-themecolor:text1;padding:0in 5.4pt 0in 5.4pt" width="189" valign="top">**<span style="color:#02E004;
  mso-themecolor:background1">Action</span>**</td>

<td style="width:4.0in;border:solid #404040 1.0pt;
  mso-border-themecolor:text1;mso-border-themetint:191;border-left:none;
  background:black;mso-background-themecolor:text1;padding:0in 5.4pt 0in 5.4pt" width="288" valign="top">**<span style="color:#02E004;
  mso-themecolor:background1">CLI Command</span>**</td>

</tr>

<tr style="mso-yfti-irow:0">

<td style="width:189.0pt;border-top:none;border-left:
  solid #404040 1.0pt;mso-border-left-themecolor:text1;mso-border-left-themetint:
  191;border-bottom:solid #404040 1.0pt;mso-border-bottom-themecolor:text1;
  mso-border-bottom-themetint:191;border-right:none;mso-border-top-alt:solid #404040 1.0pt;
  mso-border-top-themecolor:text1;mso-border-top-themetint:191;background:silver;
  mso-background-themecolor:text1;mso-background-themetint:63;padding:0in 5.4pt 0in 5.4pt" width="189" valign="top">**Login to IBM Cloud via CLI**</td>

<td style="width:4.0in;border-top:none;border-left:none;
  border-bottom:solid #404040 1.0pt;mso-border-bottom-themecolor:text1;
  mso-border-bottom-themetint:191;border-right:solid #404040 1.0pt;mso-border-right-themecolor:
  text1;mso-border-right-themetint:191;mso-border-top-alt:solid #404040 1.0pt;
  mso-border-top-themecolor:text1;mso-border-top-themetint:191;background:silver;
  mso-background-themecolor:text1;mso-background-themetint:63;padding:0in 5.4pt 0in 5.4pt" width="288" valign="top"><span class="SpellE"><span class="GramE">**<span style="mso-bidi-font-family:
  Arial;color:black">ibmcloud</span>**</span></span>** <span style="mso-bidi-font-family:Arial;color:black">login –<span class="SpellE">sso</span></span>**</td>

</tr>

<tr style="mso-yfti-irow:1">

<td style="width:189.0pt;border-top:none;border-left:
  solid #404040 1.0pt;mso-border-left-themecolor:text1;mso-border-left-themetint:
  191;border-bottom:solid #404040 1.0pt;mso-border-bottom-themecolor:text1;
  mso-border-bottom-themetint:191;border-right:none;mso-border-top-alt:solid #404040 1.0pt;
  mso-border-top-themecolor:text1;mso-border-top-themetint:191;padding:0in 5.4pt 0in 5.4pt" width="189" valign="top">**Choose the target space**</td>

<td style="width:4.0in;border-top:none;border-left:none;
  border-bottom:solid #404040 1.0pt;mso-border-bottom-themecolor:text1;
  mso-border-bottom-themetint:191;border-right:solid #404040 1.0pt;mso-border-right-themecolor:
  text1;mso-border-right-themetint:191;mso-border-top-alt:solid #404040 1.0pt;
  mso-border-top-themecolor:text1;mso-border-top-themetint:191;padding:0in 5.4pt 0in 5.4pt" width="288" valign="top"><span class="SpellE"><span class="GramE">**<span style="mso-bidi-font-family:
  Arial;color:black">ibmcloud</span>**</span></span>** <span style="mso-bidi-font-family:Arial;color:black">target --<span class="SpellE">cf</span></span>**</td>

</tr>

<tr style="mso-yfti-irow:2">

<td style="width:189.0pt;border-top:none;border-left:
  solid #404040 1.0pt;mso-border-left-themecolor:text1;mso-border-left-themetint:
  191;border-bottom:solid #404040 1.0pt;mso-border-bottom-themecolor:text1;
  mso-border-bottom-themetint:191;border-right:none;mso-border-top-alt:solid #404040 1.0pt;
  mso-border-top-themecolor:text1;mso-border-top-themetint:191;background:silver;
  mso-background-themecolor:text1;mso-background-themetint:63;padding:0in 5.4pt 0in 5.4pt" width="189" valign="top">**Create Cloud Function Action**</td>

<td style="width:4.0in;border-top:none;border-left:none;
  border-bottom:solid #404040 1.0pt;mso-border-bottom-themecolor:text1;
  mso-border-bottom-themetint:191;border-right:solid #404040 1.0pt;mso-border-right-themecolor:
  text1;mso-border-right-themetint:191;mso-border-top-alt:solid #404040 1.0pt;
  mso-border-top-themecolor:text1;mso-border-top-themetint:191;background:silver;
  mso-background-themecolor:text1;mso-background-themetint:63;padding:0in 5.4pt 0in 5.4pt" width="288" valign="top"><span class="SpellE"><span class="GramE"><span style="mso-bidi-font-family:&quot;Times Roman&quot;;color:black">ibmcloud</span></span></span> <span style="mso-bidi-font-family:&quot;Times Roman&quot;;color:black"><span class="SpellE">fn</span> action create [your cloud function name here] polyglot.zip --kind nodejs:10</span></td>

</tr>

<tr style="mso-yfti-irow:3;mso-yfti-lastrow:yes">

<td style="width:189.0pt;border-top:none;border-left:
  solid #404040 1.0pt;mso-border-left-themecolor:text1;mso-border-left-themetint:
  191;border-bottom:solid #404040 1.0pt;mso-border-bottom-themecolor:text1;
  mso-border-bottom-themetint:191;border-right:none;mso-border-top-alt:solid #404040 1.0pt;
  mso-border-top-themecolor:text1;mso-border-top-themetint:191;padding:0in 5.4pt 0in 5.4pt" width="189" valign="top">**Update Cloud Function Action**</td>

<td style="width:4.0in;border-top:none;border-left:none;
  border-bottom:solid #404040 1.0pt;mso-border-bottom-themecolor:text1;
  mso-border-bottom-themetint:191;border-right:solid #404040 1.0pt;mso-border-right-themecolor:
  text1;mso-border-right-themetint:191;mso-border-top-alt:solid #404040 1.0pt;
  mso-border-top-themecolor:text1;mso-border-top-themetint:191;padding:0in 5.4pt 0in 5.4pt" width="288" valign="top"><span class="SpellE"><span class="GramE"><span style="mso-bidi-font-family:&quot;Times Roman&quot;;color:black">ibmcloud</span></span></span> <span style="mso-bidi-font-family:&quot;Times Roman&quot;;color:black"><span class="SpellE">fn</span> action update [your cloud function name here] polyglot.zip --kind nodejs:10</span></td>

</tr>

</tbody>

</table>

**CLI command summary** **<span style="mso-fareast-font-family:Cambria;
mso-fareast-theme-font:minor-latin;mso-bidi-font-family:Cambria;mso-bidi-theme-font:
minor-latin"><span style="mso-list:Ignore">3.<span style="font:7.0pt &quot;Times New Roman&quot;"></span></span></span> ****Watson Assistant Setup and Configuration** **<span style="mso-fareast-font-family:Cambria;
mso-fareast-theme-font:minor-latin;mso-bidi-font-family:Cambria;mso-bidi-theme-font:
minor-latin"><span style="mso-list:Ignore">a.<span style="font:7.0pt &quot;Times New Roman&quot;"></span></span></span> ****Create a new Watson Assistant** **<span style="mso-fareast-font-family:Cambria;
mso-fareast-theme-font:minor-latin;mso-bidi-font-family:Cambria;mso-bidi-theme-font:
minor-latin"><span style="mso-list:Ignore">b.<span style="font:7.0pt &quot;Times New Roman&quot;"></span></span></span> ** This Watson assistant skill will be responsible for serving initial language and or target skill choices to the user as well as setting and passing pertinent variables to the cloud function. <span style="mso-fareast-font-family:Cambria;mso-fareast-theme-font:minor-latin;
mso-bidi-font-family:Cambria;mso-bidi-theme-font:minor-latin"><span style="mso-list:Ignore"><span style="font:7.0pt &quot;Times New Roman&quot;"></span>i.<span style="font:7.0pt &quot;Times New Roman&quot;"></span></span></span> Download <span style="mso-fareast-font-family:Cambria;mso-fareast-theme-font:minor-latin;
mso-bidi-font-family:Cambria;mso-bidi-theme-font:minor-latin"><span style="mso-list:Ignore"><span style="font:7.0pt &quot;Times New Roman&quot;"></span>ii.<span style="font:7.0pt &quot;Times New Roman&quot;"></span></span></span> Upload <span style="mso-fareast-font-family:Cambria;mso-fareast-theme-font:minor-latin;
mso-bidi-font-family:Cambria;mso-bidi-theme-font:minor-latin"><span style="mso-list:Ignore"><span style="font:7.0pt &quot;Times New Roman&quot;"></span>iii.<span style="font:7.0pt &quot;Times New Roman&quot;"></span></span></span> Configure <span style="mso-fareast-font-family:Cambria;mso-fareast-theme-font:minor-latin;
mso-bidi-font-family:Cambria;mso-bidi-theme-font:minor-latin"><span style="mso-list:Ignore">1.<span style="font:7.0pt &quot;Times New Roman&quot;"></span></span></span> Pattern 1, single skill <span style="mso-fareast-font-family:Cambria;mso-fareast-theme-font:minor-latin;
mso-bidi-font-family:Cambria;mso-bidi-theme-font:minor-latin"><span style="mso-list:Ignore">a.<span style="font:7.0pt &quot;Times New Roman&quot;"></span></span></span> <span class="SpellE">Webhook</span> <span style="mso-fareast-font-family:Cambria;mso-fareast-theme-font:minor-latin;
mso-bidi-font-family:Cambria;mso-bidi-theme-font:minor-latin"><span style="mso-list:Ignore">b.<span style="font:7.0pt &quot;Times New Roman&quot;"></span></span></span> Parameters <span style="mso-fareast-font-family:Cambria;mso-fareast-theme-font:minor-latin;
mso-bidi-font-family:Cambria;mso-bidi-theme-font:minor-latin"><span style="mso-list:Ignore">c.<span style="font:7.0pt &quot;Times New Roman&quot;"></span></span></span> Language translation instance <span style="mso-fareast-font-family:Cambria;mso-fareast-theme-font:minor-latin;
mso-bidi-font-family:Cambria;mso-bidi-theme-font:minor-latin"><span style="mso-list:Ignore">2.<span style="font:7.0pt &quot;Times New Roman&quot;"></span></span></span> Pattern 2, multi-skill <span style="mso-fareast-font-family:Cambria;mso-fareast-theme-font:minor-latin;
mso-bidi-font-family:Cambria;mso-bidi-theme-font:minor-latin"><span style="mso-list:Ignore">a.<span style="font:7.0pt &quot;Times New Roman&quot;"></span></span></span> <span class="SpellE">Webhook</span> <span style="mso-fareast-font-family:Cambria;mso-fareast-theme-font:minor-latin;
mso-bidi-font-family:Cambria;mso-bidi-theme-font:minor-latin"><span style="mso-list:Ignore">b.<span style="font:7.0pt &quot;Times New Roman&quot;"></span></span></span> Parameters <span style="mso-fareast-font-family:Cambria;mso-fareast-theme-font:minor-latin;
mso-bidi-font-family:Cambria;mso-bidi-theme-font:minor-latin"><span style="mso-list:Ignore">c.<span style="font:7.0pt &quot;Times New Roman&quot;"></span></span></span> Language translation instance <span style="mso-fareast-font-family:Cambria;mso-fareast-theme-font:minor-latin;
mso-bidi-font-family:Cambria;mso-bidi-theme-font:minor-latin"><span style="mso-list:Ignore">4.<span style="font:7.0pt &quot;Times New Roman&quot;"></span></span></span> Patterns and considerations <span style="mso-fareast-font-family:Cambria;mso-fareast-theme-font:minor-latin;
mso-bidi-font-family:Cambria;mso-bidi-theme-font:minor-latin"><span style="mso-list:Ignore">a.<span style="font:7.0pt &quot;Times New Roman&quot;"></span></span></span> Features <span style="mso-fareast-font-family:Cambria;mso-fareast-theme-font:minor-latin;
mso-bidi-font-family:Cambria;mso-bidi-theme-font:minor-latin"><span style="mso-list:Ignore"><span style="font:7.0pt &quot;Times New Roman&quot;"></span>i.<span style="font:7.0pt &quot;Times New Roman&quot;"></span></span></span> Maintains state <span style="mso-fareast-font-family:Cambria;mso-fareast-theme-font:minor-latin;
mso-bidi-font-family:Cambria;mso-bidi-theme-font:minor-latin"><span style="mso-list:Ignore"><span style="font:7.0pt &quot;Times New Roman&quot;"></span>ii.<span style="font:7.0pt &quot;Times New Roman&quot;"></span></span></span> Fully parameterized so you don’t need to have any coding skill. <span style="mso-fareast-font-family:Cambria;mso-fareast-theme-font:minor-latin;
mso-bidi-font-family:Cambria;mso-bidi-theme-font:minor-latin"><span style="mso-list:Ignore"><span style="font:7.0pt &quot;Times New Roman&quot;"></span>iii.<span style="font:7.0pt &quot;Times New Roman&quot;"></span></span></span> If your solution is a <span class="SpellE">wavi</span> solution remember that you cannot offer languages that aren’t supported by speech services. For a full listing of available languages please go here: <span class="GramE">issues</span> <span style="font-family:Symbol;mso-fareast-font-family:Symbol;mso-bidi-font-family:
Symbol"><span style="mso-list:Ignore">·<span style="font:7.0pt &quot;Times New Roman&quot;"></span></span></span> Notes- <span class="SpellE">Covid</span> Korean won’t work for some reason or <span class="SpellE">hindi</span> for that matter <span style="font-family:Symbol;mso-fareast-font-family:Symbol;mso-bidi-font-family:
Symbol"><span style="mso-list:Ignore">·<span style="font:7.0pt &quot;Times New Roman&quot;"></span></span></span> Also, won’t send both messages on jokes when it gives the welcome- won’t <span class="SpellE">diplay</span> the jump to message. <span style="font-family:Symbol;mso-fareast-font-family:Symbol;mso-bidi-font-family:
Symbol"><span style="mso-list:Ignore">·<span style="font:7.0pt &quot;Times New Roman&quot;"></span></span></span> Drop down lists don’t convey <span style="font-family:Symbol;mso-fareast-font-family:Symbol;mso-bidi-font-family:
Symbol"><span style="mso-list:Ignore">·<span style="font:7.0pt &quot;Times New Roman&quot;"></span></span></span> Images don’t convey Fuzzy matching on your entities must be OFF **Upload the cloud function** <span style="mso-bidi-font-family:&quot;Times Roman&quot;;
color:black"></span><span style="mso-bidi-font-family:&quot;Times Roman&quot;;
color:black">Web action <span class="SpellE"><span class="GramE">url</span></span>:</span> <span style="mso-bidi-font-family:&quot;Times Roman&quot;;
color:black">[<span style="color:#0000E9">https://us-south.functions.cloud.ibm.com/api/v1/web/ccrane%40us.ibm.com_Dev/default/languageorchestration</span>](https://us-south.functions.cloud.ibm.com/api/v1/web/ccrane%40us.ibm.com_Dev/default/languageorchestration)</span><span style="mso-bidi-font-family:&quot;Times Roman&quot;;
color:black"></span><span style="mso-bidi-font-family:&quot;Times Roman&quot;;
color:black"></span><span style="mso-bidi-font-family:&quot;Times Roman&quot;;
color:black"></span><span style="mso-bidi-font-family:&quot;Menlo Regular&quot;;
color:black"></span><span style="mso-bidi-font-family:&quot;Menlo Regular&quot;;
color:black"></span></div>
