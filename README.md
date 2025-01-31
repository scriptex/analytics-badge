# Google Analytics Badges

> https://google-analytics.atanas.info/

![Pageviews](https://google-analytics.atanas.info/api/analytics?viewId=128846594&metric=ga:pageviews&title=pageviews&style=flat&color=blue)

Node.js wrapper for a serverless service to generate Shields IO-style badges for Google Analytics. Authenticates with the Reporting API using JWT tokens and uses shields to generate badges for traffic stats. Fully customizable. Works with Universal Analytics but not GA4 properties.

## Quickstart

### Obtain service account credentials

Google service account credentials are needed for JWT authentication.

1. Visit `Google Cloud Platform > APIs & Services > Credentials` or [console.cloud.google.com/apis/credentials](https://console.cloud.google.com/apis/credentials).
2. If you do not already have a project. Click `Create project` and give it a name. Refresh the page if changes aren't showing.
3. With the project selected, add an API by clicking `Dashboard > Enable APIs and services`. Find the Google Analytics Reporting API and enable it.
4. Select `Create credentials > Service account`, give the account a name. Click continue to skip through the optional steps.
5. Click your newly created service account and scroll to the `Keys` section. Select `Add key > Create new key > JSON`, a file will be downloaded.

Your file will look like this. `client_email` and `private_key` are the values you'll need for the next steps.

```json
{
	"type": "service_account",
	"project_id": "PROJECT_ID",
	"private_key_id": "PRIVATE_KEY_ID",
	"private_key": "-----BEGIN PRIVATE KEY-----\nPRIVATE_KEY\n-----END PRIVATE KEY-----\n",
	"client_email": "CLIENT_EMAIL",
	"client_id": "CLIENT_ID",
	"auth_uri": "https://accounts.google.com/o/oauth2/auth",
	"token_uri": "https://oauth2.googleapis.com/token",
	"auth_provider_x509_cert_url": "https://www.googleapis.com/oauth2/v1/certs",
	"client_x509_cert_url": "CLIENT_CERT_URL"
}
```

### Add users to Analytics

To allow the service account access to your analytics data, you need to add it as a user. Full instructions can be found on [Google's documentation](https://support.google.com/analytics/answer/1009702?hl=en). A summary of the steps is shown below.

1. Sign in to Google Analytics. Click `Admin > Account User Management`.
2. In the Account permissions list, click +, then click Add users.
3. Enter the `client_email` for the service account. In the example file above, this would be `eightants@dummydata.gserviceaccount.com`.
4. Make sure the `Read & Analyze` permission is checked. Then click Add.

Now the setup is complete, time to deploy our serverless functions.

## Deployment

This Node.js project was built with serverless hosting in mind. However, it is also possible to integrate this into your current hosted projects.

### Integrate into existing Node server

Adapt the code in `integrate.js` into your existing server code. Full documentation about the Core Reporting API can be [found here](https://developers.google.com/analytics/devguides/reporting/core/v3/reference).

### Deploying a serverless instance

Since it requires private keys, the best way is to deploy your own instance of this service. We will be using Vercel for free serverless deployments.

[![Deploy with Vercel](https://vercel.com/button)](https://vercel.com/new/clone?repository-url=https%3A%2F%2Fgithub.com%2Fscriptex%2Fanalytics-badge&env=CLIENT_EMAIL,PRIVATE_KEY&envDescription=These%20values%20are%20defined%20in%20the%20JSON%20file%20downloaded%20from%20your%20service%20account%20&envLink=https%3A%2F%2Fgithub.com%2Fscriptex%2Fanalytics-badge%23deploying-a-serverless-instance)

_Automate steps 1-5 with the button above_

1. Fork the https://github.com/eightants/analytics-badge project to your GitHub account.
2. Create an account on Vercel.
3. From the dashboard page click Import Project then specify the URL to your fork of the project on GitHub.
4. Add the required environment variables CLIENT_EMAIL and PRIVATE_KEY. These values correspond to `client-email` and `private-key` above.
5. Deploy and visit your application at `<deploy-id>.vercel.app` or the custom domain which you configured.

Visit your Google Analytics dashboard and obtain the `viewId` for your project of interest from `Admin > View > View Settings > View ID`. You can now generate shields.io badges for any of your Analytics projects with this endpoint.

```
https://<deploy-id>.vercel.app/api/analytics?viewId=<VIEWID>&metric=<METRIC>&startDate=<STARTDATE>&endDate=<ENDDATE>&title=<TITLE>&unit=<UNIT>&color=<COLOR>&style=<STYLE>&si=<SI>
```

**Query Parameters**
| Param | Description |
| ----------- | ----------- |
| viewId | (Required) The View ID of your Google Analytics property |
| metric | The metric id as specified in [Google dev tools](https://ga-dev-tools.appspot.com/dimensions-metrics-explorer/). Default: `ga:users` |
| startDate | Start date for fetching Analytics data. Requests can specify a start date formatted as `YYYY-MM-DD`, or as a relative date (e.g., `today`, `yesterday`, or `NdaysAgo` where `N` is a positive integer). Default: `7daysAgo`|
| endDate | End date for fetching Analytics data. Requests can specify a start date formatted as `YYYY-MM-DD`, or as a relative date (e.g., `today`, `yesterday`, or `NdaysAgo` where `N` is a positive integer). Default: `today`|
| title | Title for Shields.io badge. Default: `users` |
| unit | Unit of metric displayed on badge, e.g. 20k/week, 300/month. Special characters need to be URL encoded. Default: `%2Fweek` |
| color | Color of shield. Default: `green` |
| style | Shield style according to Shields.io documentation. Default: `plastic` |
| si | Whether to use SI abbreviations for numbers, e.g. 20k, 4M, 8B. Default: `true` |

## Examples

Using analytics data from my project Whisperify.

### Default shield for users per week

In Markdown: `![Users](https://<deploy-id>.vercel.app/api/analytics?viewId=211113681)`

In HTML: `<img src="https://<deploy-id>.vercel.app/api/analytics?viewId=211113681"/>`

![Users Shield](https://google-analytics.atanas.info/api/analytics?viewId=128846594)

### Pageviews per month

`https://<deploy-id>.vercel.app/api/analytics?viewId=211113681&metric=ga:pageviews&startDate=30daysAgo&title=pageviews&unit=%2Fmonth`

![Views Shield](https://google-analytics.atanas.info/api/analytics?viewId=128846594&metric=ga:pageviews&startDate=30daysAgo&title=pageviews&unit=%2Fmonth)

### Bounce Rate over the past day with badge styles

`https://<deploy-id>.vercel.app/api/analytics?viewId=211113681&metric=ga:bounceRate&startDate=yesterday&title=Bounce%20Rate&unit=%25&style=for-the-badge&color=blue`

![Shield](https://google-analytics.atanas.info/api/analytics?viewId=128846594&metric=ga:bounceRate&startDate=yesterday&title=Bounce%20Rate&unit=%25&color=blue)

## Contribute

Feel free to contribute corrections in this guide or provide documentation on how to set up this project on other serverless platforms such as GCP or AWS Lambda.

## LICENSE

MIT

---

<div align="center">
    Connect with me:
</div>

<br />

<div align="center">
    <a href="https://atanas.info">
        <img src="https://raw.githubusercontent.com/scriptex/socials/master/styled-assets/logo.svg" height="20" alt="">
    </a>
    &nbsp;
    <a href="mailto:hi@atanas.info">
        <img src="https://raw.githubusercontent.com/scriptex/socials/master/styled-assets/email.svg" height="20" alt="">
    </a>
    &nbsp;
    <a href="https://www.linkedin.com/in/scriptex/">
        <img src="https://raw.githubusercontent.com/scriptex/socials/master/styled-assets/linkedin.svg" height="20" alt="">
    </a>
    &nbsp;
    <a href="https://github.com/scriptex">
        <img src="https://raw.githubusercontent.com/scriptex/socials/master/styled-assets/github.svg" height="20" alt="">
    </a>
    &nbsp;
    <a href="https://gitlab.com/scriptex">
        <img src="https://raw.githubusercontent.com/scriptex/socials/master/styled-assets/gitlab.svg" height="20" alt="">
    </a>
    &nbsp;
    <a href="https://twitter.com/scriptexbg">
        <img src="https://raw.githubusercontent.com/scriptex/socials/master/styled-assets/twitter.svg" height="20" alt="">
    </a>
    &nbsp;
    <a href="https://www.npmjs.com/~scriptex">
        <img src="https://raw.githubusercontent.com/scriptex/socials/master/styled-assets/npm.svg" height="20" alt="">
    </a>
    &nbsp;
    <a href="https://www.youtube.com/user/scriptex">
        <img src="https://raw.githubusercontent.com/scriptex/socials/master/styled-assets/youtube.svg" height="20" alt="">
    </a>
    &nbsp;
    <a href="https://stackoverflow.com/users/4140082/atanas-atanasov">
        <img src="https://raw.githubusercontent.com/scriptex/socials/master/styled-assets/stackoverflow.svg" height="20" alt="">
    </a>
    &nbsp;
    <a href="https://codepen.io/scriptex/">
        <img src="https://raw.githubusercontent.com/scriptex/socials/master/styled-assets/codepen.svg" width="20" alt="">
    </a>
    &nbsp;
    <a href="https://profile.codersrank.io/user/scriptex">
        <img src="https://raw.githubusercontent.com/scriptex/socials/master/styled-assets/codersrank.svg" height="20" alt="">
    </a>
    &nbsp;
    <a href="https://linktr.ee/scriptex">
        <img src="https://raw.githubusercontent.com/scriptex/socials/master/styled-assets/linktree.svg" height="20" alt="">
    </a>
</div>

---

<div align="center">
Support and sponsor my work:
<br />
<br />
<a href="https://twitter.com/intent/tweet?text=Checkout%20this%20awesome%20developer%20profile%3A&url=https%3A%2F%2Fgithub.com%2Fscriptex&via=scriptexbg&hashtags=software%2Cgithub%2Ccode%2Cawesome" title="Tweet">
	<img src="https://img.shields.io/badge/Tweet-Share_my_profile-blue.svg?logo=twitter&color=38A1F3" />
</a>
<a href="https://paypal.me/scriptex" title="Donate on Paypal">
	<img src="https://img.shields.io/badge/Donate-Support_me_on_PayPal-blue.svg?logo=paypal&color=222d65" />
</a>
<a href="https://revolut.me/scriptex" title="Donate on Revolut">
	<img src="https://img.shields.io/endpoint?url=https://raw.githubusercontent.com/scriptex/scriptex/master/badges/revolut.json" />
</a>
<a href="https://patreon.com/atanas" title="Become a Patron">
	<img src="https://img.shields.io/badge/Become_Patron-Support_me_on_Patreon-blue.svg?logo=patreon&color=e64413" />
</a>
<a href="https://ko-fi.com/scriptex" title="Buy Me A Coffee">
	<img src="https://img.shields.io/badge/Donate-Buy%20me%20a%20coffee-yellow.svg?logo=ko-fi" />
</a>
<a href="https://liberapay.com/scriptex/donate" title="Donate on Liberapay">
	<img src="https://img.shields.io/liberapay/receives/scriptex?label=Donate%20on%20Liberapay&logo=liberapay" />
</a>

<a href="https://img.shields.io/endpoint?url=https://raw.githubusercontent.com/scriptex/scriptex/master/badges/bitcoin.json" title="Donate Bitcoin">
	<img src="https://img.shields.io/endpoint?url=https://raw.githubusercontent.com/scriptex/scriptex/master/badges/bitcoin.json" />
</a>
<a href="https://img.shields.io/endpoint?url=https://raw.githubusercontent.com/scriptex/scriptex/master/badges/etherium.json" title="Donate Etherium">
	<img src="https://img.shields.io/endpoint?url=https://raw.githubusercontent.com/scriptex/scriptex/master/badges/etherium.json" />
</a>
<a href="https://img.shields.io/endpoint?url=https://raw.githubusercontent.com/scriptex/scriptex/master/badges/shiba-inu.json" title="Donate Shiba Inu">
	<img src="https://img.shields.io/endpoint?url=https://raw.githubusercontent.com/scriptex/scriptex/master/badges/shiba-inu.json" />
</a>
</div>
