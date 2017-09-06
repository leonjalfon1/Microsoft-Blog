1) Access to the Linux machine and open the terminal (Ctrl+Alt+T)

<img src="https://github.com/leonjalfon1/Microsoft-Blog/blob/master/AccessLinuxFromWinRD/Images/AccessLinuxFromWinRD_1.png?raw=true" alt="AccessLinuxFromWinRD_1.png" />

&nbsp;

2) Update the apt and install xrdp using the following command:

- -&gt; sudo apt-get update

<img src="https://github.com/leonjalfon1/Microsoft-Blog/blob/master/AccessLinuxFromWinRD/Images/AccessLinuxFromWinRD_2.png?raw=true" alt="AccessLinuxFromWinRD_2.png" />

&nbsp;

3) Install xrdp using the following command:

--&gt; sudo apt-get install xrdp –y

<img src="https://github.com/leonjalfon1/Microsoft-Blog/blob/master/AccessLinuxFromWinRD/Images/AccessLinuxFromWinRD_3.png?raw=true" alt="AccessLinuxFromWinRD_3.png" />

&nbsp;

4) Install xfce4 with the command below:

--&gt; sudo apt-get install xfce4 –y

<img src="https://github.com/leonjalfon1/Microsoft-Blog/blob/master/AccessLinuxFromWinRD/Images/AccessLinuxFromWinRD_4.png?raw=true" alt="AccessLinuxFromWinRD_4.png" />

&nbsp;

5) Run the following command to make sure xRDP uses xfce4 (step 1 of 2):

--&gt; echo xfce4-session &gt;~/.xsession

<img src="https://github.com/leonjalfon1/Microsoft-Blog/blob/master/AccessLinuxFromWinRD/Images/AccessLinuxFromWinRD_5.png?raw=true" alt="AccessLinuxFromWinRD_5.png" />

&nbsp;

6) Run the following command to make sure xRDP uses xfce4 (step 2 of 2):

--&gt; sudo nano /etc/xrdp/startwm.sh

<img src="https://github.com/leonjalfon1/Microsoft-Blog/blob/master/AccessLinuxFromWinRD/Images/AccessLinuxFromWinRD_6.png?raw=true" alt="AccessLinuxFromWinRD_6.png" />

&nbsp;

7) Add the following line before the last line of the file

--&gt; startxfce4

<img src="https://github.com/leonjalfon1/Microsoft-Blog/blob/master/AccessLinuxFromWinRD/Images/AccessLinuxFromWinRD_7.png?raw=true" alt="AccessLinuxFromWinRD_7.png" />

&nbsp;

8) Then, click “Ctrl+X”, click “Y” and click “Enter” to save the changes

<img src="https://github.com/leonjalfon1/Microsoft-Blog/blob/master/AccessLinuxFromWinRD/Images/AccessLinuxFromWinRD_8.png?raw=true" alt="AccessLinuxFromWinRD_8.png" />

&nbsp;

9) Use the command below to make all these changes effective

--&gt; sudo service xrdp restart

<img src="https://github.com/leonjalfon1/Microsoft-Blog/blob/master/AccessLinuxFromWinRD/Images/AccessLinuxFromWinRD_9.png?raw=true" alt="AccessLinuxFromWinRD_9.png" />

&nbsp;

10) Open “Remote Desktop” write the machine IP and click connect

<img src="https://github.com/leonjalfon1/Microsoft-Blog/blob/master/AccessLinuxFromWinRD/Images/AccessLinuxFromWinRD_10.png?raw=true" alt="AccessLinuxFromWinRD_10.png" />

&nbsp;

11) Enter your credentials and click “OK”

<img src="https://github.com/leonjalfon1/Microsoft-Blog/blob/master/AccessLinuxFromWinRD/Images/AccessLinuxFromWinRD_11.png?raw=true" alt="AccessLinuxFromWinRD_11.png" />

&nbsp;

12) Now you are connected to the Linux machine using the windows remote desktop (although it may look a little different)

<img src="https://github.com/leonjalfon1/Microsoft-Blog/blob/master/AccessLinuxFromWinRD/Images/AccessLinuxFromWinRD_12.png?raw=true" alt="AccessLinuxFromWinRD_12.png" />

&nbsp;
