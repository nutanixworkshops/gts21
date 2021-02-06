.. _euc_secure:

---------------------------
Securing Desktops with Flow
---------------------------

This exercise depends on completion of the **Hybrid Cloud IaaS** :ref:`assign_categories` exercise to properly categorize VMs that will be used in Flow policies. Refer to the linked section first before proceeding.

In this exercise...


Configuring ID Based Security
+++++++++++++++++++++++++++++

Configuring these settings only needs to be done once per shared environment, if the **Bootcamp Users** and **SSP Developers** user groups have already been added, review the configuration and skip to :ref:`euc_flowpolicy`.

#. In **Prism Central**, select :fa:`bars` **> Prism Central Settings**.

#. Under **Flow**, select **ID Based Security**.

#. If **No Active Directory Domains** have been added, click **Use Existing AD**.

   .. figure:: images/1.png

#. Select **NTNXLAB** from the **AD Server** dropdown and provide the **NTNXLAB\\Administrator** password as the **Service Account Password**.

   .. figure:: images/2.png

#. Click **Next**.

#. Click **Manually add Domain Controllers**.

   The IP of the primary domain controller (ex. 10.X.X.41) should be automatically populated.

#. Click :fa:`check-circle` to confirm the addition of your domain controller.

   .. figure:: images/3.png

#. Click **Save**.

#. Under **Referenced AD Groups**, click **+ Add User Group**.

#. Specify **Bootcamp Users** as the **User Group** and click :fa:`check-circle`.

   .. figure:: images/4.png

#. Repeat **Steps 9-10** to add the **SSP Developers** user group.

   .. figure:: images/5.png

.. _euc_flowpolicy:

Adding The Flow VDI Policy
++++++++++++++++++++++++++

Configuring these settings only needs to be done once per shared environment, if the **VDI Policy** already exists, review the steps and proceed to :ref:`euc_flowpolicy2`.

#. In **Prism Central**, select :fa:`bars` **> Policies > Security**.

#. Click **Create Security Policy** and select **Secure VDI Groups (VDI Policy)**.

   .. figure:: images/6.png

#. Click **Create**.

#. Select **Include all VMs**.

   .. figure:: images/13.png

   Typically you would select **Include VMs by name** and specify whatever portion of your VM naming scheme is shared across all your virtual desktops (ex. VDI or CTX). As the shared environment lacks this consistency, we will target ALL VMs. This will ensure all VMs will be subject to AD logon processing by Prism Central to dynamically assign its **ADGroup** category.

#. Select **Add these VMs to a default policy**.

   .. figure:: images/7.png

#. Click **Next**.

#. Click **Import all AD Groups** to add all AD groups configured in **Prism Central ID Based Security** to the policy.

#. Explore the policy builder and observe, similar to a Flow App Policy, it is possible to whitelist inbound and outbound traffic based on IP, subnet, or Nutanix category.

   .. figure:: images/8.png

   Additionally, when mapping inbound or outbound rules to **ADGroups**, you can further narrow your policy by defining specify services based on protocol and port.

   .. figure:: images/10.png

#. After experimenting, click **Next**.

#. Ensure **Monitor** is selected as the **Policy mode** before clicking **Save and Monitor**.

   .. figure:: images/9.png

   *DO NOT ENFORCE THIS POLICY AS IT COULD NEGATIVELY IMPACT OTHER USERS ON THE CLUSTER*.

   .. figure:: https://media.giphy.com/media/yAcKHAu1iFdTvOysZK/giphy.gif

.. _euc_flowpolicy2:

Creating A User Based Isolation Policy
++++++++++++++++++++++++++++++++++++++

#. In **Prism Central**, select :fa:`bars` **> Policies > Security**.

#. Click **Create Security Policy** and select **Isolate Environments (Isolation Policy)**.

#. Click **Create**.

#. Fill out the following fields:

   - **Name** - USER\ *##*\ -UserIsolation (ex. USER01-ADIsolation)
   - **Purpose** - Blacklisting NTNXLAB Bootcamp Users from category
   - **Isolate this category** - ADGroup:Bootcamp Users
   - **From this category** - User:\ *##* (ex. USER:01)
   - **Select a Policy mode** - Monitor

   .. figure:: images/11.png

   .. note::

      This assumes you have already added the **USER:**\ *##* category to your **USER**\ *##*\ **-FiestaWeb** and **USER**\ *##*\ **-MSSQL-Source** VMs, completed as part of **Hybrid Cloud IaaS** :ref:`assign_categories`.

      If you have not completed this exercise, refer to the linked steps for instruction on how to add the category to the aforementioned VMs.

#. Click **Save and Monitor**.

#. Return to your **USER**\ *##*\ **-WinTools** VM.

#. Sign out of any Citrix desktop sessions you may have left open.

#. Open http://ddc.ntnxlab.local/Citrix/NTNXLABWeb/ and login as **NTNXLAB\\user**\ *##* (ex. NTNXLAB\\user01).

#. Launch your Windows 10 desktop and wait for login to complete.

#. Attempt to ping the IP of your **USER**\ *##*\ **-FiestaWeb** or access the IP via your browser. You should expect the connection to succeed as you have not yet enforced your policy.

   Note the hostname of your desktop VM.

   .. figure:: images/12.png

#. Return to your **USER**\ *##*\ **-UserIsolation** policy in **Prism Central** and observe that traffic flows have been discovered, and your VM has been automatically added to the **ADGroup:Bootcamp Users** category.

   .. figure:: images/14.png

#. Click **Enforce** to start enforcing the policy and return to your desktop to verify you no longer have access to **USER**\ *##*\ **-FiestaWeb**.

   .. figure:: images/15.png

   That's it! Optionally, you can continue logging into desktops as your **user** and **devuser** accounts and validate that categories and policy are applied as expected - whether on-prem or in the cloud!

Takeaways
+++++++++

-