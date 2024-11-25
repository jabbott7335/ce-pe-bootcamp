The aliases and firewall rules for this exercise are available in the Box folder. When prompted to select a file in the steps below, choose the file with the name that matches the data center of your environment.

1. Open a browser and navigate to https://pfsense.gym.lan{: target="_blank" .external }.

    Login as user `admin` with the password from the "Shared Reservation" section of your reservation.

1. Load the aliases and firewall rules.

    1. Navigate to **Diagnostics > Backup & Restore**.
    
    1. In the Restore Backup section select **Aliases** from the **Restore area** drop-down box
    
    1. Click the button **Choose file** in the Configuration file field
    
    1. Select the file and click **Open**
    
    1. Click **Restore Configuration** and confirm by clicking **OK**
    
    1. In the Restore Backup section select **Firewall Rules** from the **Restore area** drop-down box
    
    1. Click the button **Choose file** in the Configuration file field
    
    1. Select the file and click **Open**
    
    1. Click **Restore Configuration** and confirm by clicking **OK**

        Note that it is not necessary to reboot pfsense.

1. Check the firewall rules.

    1. Navigate to **Firewall** > **Rules**

    1. Click the **LAN** tab

        <figure markdown="span">
            <figcaption>Reference Firewall Rules for pfsense</figcaption>
            ![eference Firewall Rules for pfsense](images/firewall-rules.png)
        </figure>
