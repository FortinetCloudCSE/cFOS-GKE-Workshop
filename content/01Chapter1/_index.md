---
title: "Chapter 1 - Getting Started"
weight: 1
weight: 10
---

## Fortinet GKE cFOS Workshop

### Chapter 1 - Provisioning the Google Cloud environment (40min)

Provision your Google Cloud Environment, enter your Email address and click _Provision_

<script>
    function formSubmit() {
      alert('Called formSubmit1');
      fetch ('https://us-central1-cse-us-341516.cloudfunctions.net/gcp-account-provision-test', {
        method: 'POST',
        headers: {
          'Content-Type': 'application/json'
        },
        body: JSON.stringify({
          'email': document.getElementById("useremail").value, 
          'workshop': 'gke-cfos-workshop'
        })
      })
      .then(response => response.text())
      .then(data => {
        alert(data)
        console.log(data);
      })
      .catch(error => {
        alert('Error:' +error)
        console.error('Error', error)
      });
    }
  </script>
<form id="provision-lab" onsubmit="return formSubmit();">
    <label for="useremail">Please enter your email address</label>
    <input type="email" id="useremail" name="useremail" value="" />
    <button type="submit">Provision</button>
</form>


{{< notice warning >}} After submitting, this page will return with a blank email address box and no other indications.

Provisioning can take several minutes.

\*\*\* __PLEASE DO NOT SUBMIT MULTIPLE TIMES__ \*\*\*  {{< /notice >}}

When provisioning is complete, one of the following will happen.

* You will receive an email with Google Cloud environment credentials. Use those credentials for this environment, even if you have your own.
* You will receive and email indicating that there are no environments available to utilize. In this case please try again at a later date.
* You will receive an email indicating that the supplied email address is from an unsupported domain.
* No email received due to an unexpected error. You can try again or notify the Google Cloud CSE team.

Tasks

* Validate Project & APIs
* Setup Google Cloud Shell
* Create Docker Image for cFOS