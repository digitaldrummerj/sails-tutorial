## CORS Configuration

Before we can use our API against a UI or allow others to call the API we need to configure CORS.

CORS allows us to configure which routes, domains, if cookies are passed in, which REST verbs are allowed, and what header values are allowed.  

<h4 class="exercise-start">
    <b>Exercise</b>: CORS Config
</h4>


```javascript
  /***************************************************************************
  *                                                                          *
  * Allow CORS on all routes by default? If not, you must enable CORS on a   *
  * per-route basis by either adding a "cors" configuration object to the    *
  * route config, or setting "cors:true" in the route config to use the      *
  * default settings below.                                                  *
  *                                                                          *
  ***************************************************************************/

  // allRoutes: false,

  /***************************************************************************
  *                                                                          *
  * Which domains which are allowed CORS access? This can be a               *
  * comma-delimited list of hosts (beginning with http:// or https://) or    *
  * "*" to allow all domains CORS access.                                    *
  *                                                                          *
  ***************************************************************************/

  // origin: '*',

  /***************************************************************************
  *                                                                          *
  * Allow cookies to be shared for CORS requests?                            *
  *                                                                          *
  ***************************************************************************/

  // credentials: true,

  /***************************************************************************
  *                                                                          *
  * Which methods should be allowed for CORS requests? This is only used in  *
  * response to preflight requests (see article linked above for more info)  *
  *                                                                          *
  ***************************************************************************/

  // methods: 'GET, POST, PUT, DELETE, OPTIONS, HEAD',

  /***************************************************************************
  *                                                                          *
  * Which headers should be allowed for CORS requests? This is only used in  *
  * response to preflight requests.                                          *
  *                                                                          *
  ***************************************************************************/

  // headers: 'content-type'


```

<div class="exercise-end"></div>
