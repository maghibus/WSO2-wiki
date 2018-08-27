<h1 id="integrazione--openid-wso2">Integrazione  OpenID WSO2</h1>
<p>Scaricare wso2is 5.3.0<br>
Creare un utente e un Service Provider con callback URL <a href="http://localhost:8080">http://localhost:8080</a></p>
<p>Scaricare un connettore certificato OpenID per applicazioni Angular:  <a href="https://github.com/damienbod/angular-auth-oidc-client">angular-auth-oidc-client</a></p>
<pre><code>$ git clone https://github.com/damienbod/angular-auth-oidc-client.git
</code></pre>
<p>Lavorare con l’ultima release stabile:</p>
<pre><code>$ cd angular-auth-oidc-client
$ git checkout tags/release_6_0_6
</code></pre>
<p>Installare le dipendenze e compilare:</p>
<pre><code>$ npm install
$ npm run build
</code></pre>
<p>Generare il pacchetto <strong>angular-auth-oidc-client-6.0.6.tgz</strong>:</p>
<pre><code>$ npm run pack-lib
</code></pre>
<p>Scaricare un’applicazione client Angular:  <a href="https://github.com/damienbod/angular-auth-oidc-sample-google-openid">angular-auth-oidc-sample-google-openid</a></p>
<p>Impostare la dipendenza al connettore OpenId:</p>
<pre class=" language-json"><code class="prism  language-json"> <span class="token string">"dependencies"</span><span class="token punctuation">:</span> <span class="token punctuation">{</span>
	<span class="token operator">...</span>
    <span class="token string">"angular-auth-oidc-client"</span><span class="token punctuation">:</span> <span class="token string">"file:../../../angular-auth-oidc-client/angular-auth-oidc-client-6.0.6.tgz"</span><span class="token punctuation">,</span>
	<span class="token operator">...</span>
  <span class="token punctuation">}</span><span class="token punctuation">,</span>
</code></pre>
<p>Installare le dipendenze:</p>
<pre><code>$ cd \angular-auth-oidc-sample-google-openid\src\AngularClient
$ npm install
</code></pre>
<p>Configurare il server wso2 in <em>app.module.ts</em></p>
<pre class=" language-ts"><code class="prism  language-ts"><span class="token operator">...</span>
<span class="token keyword">export</span> <span class="token keyword">function</span> <span class="token function">loadConfig</span><span class="token punctuation">(</span>oidcConfigService<span class="token punctuation">:</span> OidcConfigService<span class="token punctuation">)</span> <span class="token punctuation">{</span>
    console<span class="token punctuation">.</span><span class="token function">log</span><span class="token punctuation">(</span><span class="token string">'APP_INITIALIZER STARTING'</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token keyword">return</span> <span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token operator">=&gt;</span> oidcConfigService<span class="token punctuation">.</span><span class="token function">load_using_stsServer</span><span class="token punctuation">(</span><span class="token string">'https://localhost:9443/oauth2/oidcdiscovery'</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token punctuation">}</span>
<span class="token operator">...</span>
<span class="token keyword">export</span> <span class="token keyword">class</span> <span class="token class-name">AppModule</span> <span class="token punctuation">{</span>
    <span class="token keyword">constructor</span><span class="token punctuation">(</span>
        <span class="token keyword">private</span> oidcSecurityService<span class="token punctuation">:</span> OidcSecurityService<span class="token punctuation">,</span>
        <span class="token keyword">private</span> oidcConfigService<span class="token punctuation">:</span> OidcConfigService<span class="token punctuation">,</span>
    <span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token keyword">this</span><span class="token punctuation">.</span>oidcConfigService<span class="token punctuation">.</span>onConfigurationLoaded<span class="token punctuation">.</span><span class="token function">subscribe</span><span class="token punctuation">(</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token operator">=&gt;</span> <span class="token punctuation">{</span>
        <span class="token keyword">let</span> openIDImplicitFlowConfiguration <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">OpenIDImplicitFlowConfiguration</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
        openIDImplicitFlowConfiguration<span class="token punctuation">.</span>stsServer <span class="token operator">=</span> <span class="token string">'https://localhost:9443/oauth2/token'</span><span class="token punctuation">;</span>
        openIDImplicitFlowConfiguration<span class="token punctuation">.</span>redirect_url <span class="token operator">=</span> <span class="token string">'http://localhost:8080'</span><span class="token punctuation">;</span>
        openIDImplicitFlowConfiguration<span class="token punctuation">.</span>client_id <span class="token operator">=</span> <span class="token string">''</span><span class="token punctuation">;</span>
        openIDImplicitFlowConfiguration<span class="token punctuation">.</span>response_type <span class="token operator">=</span> <span class="token string">'id_token token'</span><span class="token punctuation">;</span>
        openIDImplicitFlowConfiguration<span class="token punctuation">.</span>scope <span class="token operator">=</span> <span class="token string">'openid email profile'</span><span class="token punctuation">;</span>
        openIDImplicitFlowConfiguration<span class="token punctuation">.</span>post_logout_redirect_uri <span class="token operator">=</span> <span class="token string">'http://localhost:8080'</span><span class="token punctuation">;</span>
        openIDImplicitFlowConfiguration<span class="token punctuation">.</span>post_login_route <span class="token operator">=</span> <span class="token string">'/home'</span><span class="token punctuation">;</span>
        openIDImplicitFlowConfiguration<span class="token punctuation">.</span>forbidden_route <span class="token operator">=</span> <span class="token string">'/Forbidden'</span><span class="token punctuation">;</span>
        openIDImplicitFlowConfiguration<span class="token punctuation">.</span>unauthorized_route <span class="token operator">=</span> <span class="token string">'/Unauthorized'</span><span class="token punctuation">;</span>
        openIDImplicitFlowConfiguration<span class="token punctuation">.</span>trigger_authorization_result_event <span class="token operator">=</span> <span class="token keyword">true</span><span class="token punctuation">;</span>
        openIDImplicitFlowConfiguration<span class="token punctuation">.</span>log_console_warning_active <span class="token operator">=</span> <span class="token keyword">true</span><span class="token punctuation">;</span>
        openIDImplicitFlowConfiguration<span class="token punctuation">.</span>log_console_debug_active <span class="token operator">=</span> <span class="token keyword">true</span><span class="token punctuation">;</span>
        openIDImplicitFlowConfiguration<span class="token punctuation">.</span>max_id_token_iat_offset_allowed_in_seconds <span class="token operator">=</span> <span class="token number">20</span><span class="token punctuation">;</span>
        <span class="token keyword">const</span> authWellKnownEndpoints <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">AuthWellKnownEndpoints</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
        authWellKnownEndpoints<span class="token punctuation">.</span><span class="token function">setWellKnownEndpoints</span><span class="token punctuation">(</span><span class="token keyword">this</span><span class="token punctuation">.</span>oidcConfigService<span class="token punctuation">.</span>wellKnownEndpoints<span class="token punctuation">)</span><span class="token punctuation">;</span>
        <span class="token keyword">this</span><span class="token punctuation">.</span>oidcSecurityService<span class="token punctuation">.</span><span class="token function">setupModule</span><span class="token punctuation">(</span>openIDImplicitFlowConfiguration<span class="token punctuation">,</span> authWellKnownEndpoints<span class="token punctuation">)</span><span class="token punctuation">;</span>
        <span class="token punctuation">}</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
        console<span class="token punctuation">.</span><span class="token function">log</span><span class="token punctuation">(</span><span class="token string">'APP STARTING'</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span>
    <span class="token operator">...</span>
</code></pre>
<h2 id="claim-mapping">Claim mapping</h2>
<p>La configurazione dei claim per il service provider è la seguente<br>
<img src="https://lh3.googleusercontent.com/qbPqNqU1DKjbTNofGrhUEyLdwVX-2CmHo4IFa02bVyxZ47hpWqB5gzKqOmz_yko5Xl-Q_qTrZSzd" alt="enter image description here"></p>
<pre class=" language-json"><code class="prism  language-json"><span class="token punctuation">{</span>
  <span class="token string">"at_hash"</span><span class="token punctuation">:</span> <span class="token string">"YAXQhRbyXhge39bzACEDBQ"</span><span class="token punctuation">,</span>
  <span class="token string">"sub"</span><span class="token punctuation">:</span> <span class="token string">"garibaldi"</span><span class="token punctuation">,</span>
  <span class="token string">"iss"</span><span class="token punctuation">:</span> <span class="token string">"https://elasticazldev03:9443/oauth2/token"</span><span class="token punctuation">,</span>
  <span class="token string">"nonce"</span><span class="token punctuation">:</span> <span class="token string">"N0.64362727056036581535364139480"</span><span class="token punctuation">,</span>
  <span class="token string">"aud"</span><span class="token punctuation">:</span> <span class="token punctuation">[</span>
    <span class="token string">"2sv5apSqyfhEy0mVKPG3pMRaf0ka"</span>
  <span class="token punctuation">]</span><span class="token punctuation">,</span>
  <span class="token string">"azp"</span><span class="token punctuation">:</span> <span class="token string">"2sv5apSqyfhEy0mVKPG3pMRaf0ka"</span><span class="token punctuation">,</span>
  <span class="token string">"auth_time"</span><span class="token punctuation">:</span> <span class="token number">1535364145</span><span class="token punctuation">,</span>
  <span class="token string">"name"</span><span class="token punctuation">:</span> <span class="token string">"Francesco Salvatore"</span><span class="token punctuation">,</span>
  <span class="token string">"exp"</span><span class="token punctuation">:</span> <span class="token number">1535367747</span><span class="token punctuation">,</span>
  <span class="token string">"iat"</span><span class="token punctuation">:</span> <span class="token number">1535364147</span><span class="token punctuation">,</span>
  <span class="token string">"email"</span><span class="token punctuation">:</span> <span class="token string">"garibaldi@unita.it"</span>
<span class="token punctuation">}</span>
</code></pre>
<h2 id="start">Start</h2>
<p>Avviare l’applicazione:</p>
<pre><code>$ npm start
</code></pre>

