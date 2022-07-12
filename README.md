<article>
  <div class="content">
    <div class="header">
      <div class="col-xs-12 breadcrumb-padding">
        <h2>How to Spider a Site With JMeter</h2>
      </div>
      <div>
        <div class="content-html">
          <p pid="2">When it comes to building a web application load test, you might want to simulate a group of users “crawling” the website and randomly clicking the links. Personally, I don’t like this form of testing, as I believe that the load test needs to be repeatable, so each consecutive test hits the same endpoints and assumes the same throughput as the previous one. If the entry criteria is different, it’s harder to analyze the load test results.</p>
          <p pid="4">Sometimes this rule isn’t applicable, especially for dynamic websites like blogs, news portals, social networks, etc., where new content is being added very often or even in real time. This form of testing ensures the user will get a smooth browsing experience and also checks for broken links or any unexpected errors.</p>
          <p pid="6">This article covers the 3 most common approaches of simulating website “crawling”: clicking all links found in the web page, using HTML Link Parser and the advanced spidering test plan.&nbsp;</p>
          <h2>1. Clicking All Links Found in the Web Page</h2>
          <p pid="8">The process of getting the links using Regular Expression Extractor is described in the <a href="https://www.blazemeter.com/blog/using-regular-expressions-jmeter?utm_source=BM&amp;utm_medium=BM_blog&amp;utm_campaign=spider-site-jmeter-tutorial" rel="nofollow" target="_blank" style="background-color: rgb(255, 255, 255); line-height: 1.45;">Using Regular Expressions in JMeter</a> article. The algorithm is as simple as this: </p>
          <p pid="10">1a. Extracting all the links from the response with the <a href="http://jmeter.apache.org/usermanual/component_reference.html#Regular_Expression_Extractor" rel="nofollow" target="_blank" style="background-color: rgb(255, 255, 255); line-height: 1.45;">Regular Expression Extractor</a> and store them into JMeter Variables. The relevant regular expression would be something like: </p>
          <p pid="12" style="margin-left: 20px;">
            <strong>&nbsp;&lt;a[^&gt;]* href="([^"]*)"</strong>
          </p>
          <p pid="14">Don’t forget to set <strong>Match No.</strong> to <strong style="line-height: 1.45;">-1</strong> to extract all links. If you leave it blank, only the first match will be returned. </p>
          <p pid="17">
            <img alt="regular expression extractor jmeter" class="fr-fin fr-dib" src="http://cdn2.hubspot.net/hubfs/208250/Blog_Images/spider1.png">
          </p>
          <p pid="18">1b. Use <a href="http://jmeter.apache.org/usermanual/component_reference.html#ForEach_Controller" rel="nofollow" target="_blank" style="background-color: rgb(255, 255, 255); line-height: 1.45;">ForEach Controller</a> to iterate the extracted links. </p>
          <p pid="20">1c. Use HTTP Request sampler to hit the URL living in the <strong>Output Variable Name</strong>. </p>
          <h3>Demo</h3>
          <p pid="24">
            <img alt="regular expression extractor jmeter demo" class="fr-fil fr-dib" src="http://cdn2.hubspot.net/hubfs/208250/Blog_Images/spider2.png">
          </p>
          <h3 pid="25">&nbsp;Pros</h3>
          <ul>
            <li>
              <p pid="26">Simplicity of configuration.</p>
            </li>
            <li>
              <p pid="26">Stability.</p>
            </li>
            <li>
              <p pid="26">Failover and resilience .</p>
            </li>
          </ul>
          <h3>Cons</h3>
          <ul>
            <li>
              <p pid="30">Regular Expressions are hard to develop and sensitive to markup change hence fragile.</p>
            </li>
            <li>
              <p pid="30">Not actually a “crawler” or “spider,” just consequential requests to links.</p>
            </li>
          </ul>
          <h2>2. Using HTML Link Parser</h2>
          <p pid="33">&nbsp;JMeter provides a special Test Element,&nbsp; <a href="http://jmeter.apache.org/usermanual/component_reference.html#HTML_Link_Parser" rel="nofollow" target="_blank" style="background-color: rgb(255, 255, 255); line-height: 1.45;">HTML Link Parser</a>. This element is designed for extracting HTML links and forms and substituting matching HTTP Request Sampler relevant fields with the extracted values. Therefore, HTML Link Parser can be used to simulate crawling the website with minimal configuration. Here’s how: </p>
          <p pid="35">2a. Put the HTML Link Parser under a <a href="http://jmeter.apache.org/usermanual/component_reference.html#logic_controllers" rel="nofollow" target="_blank" style="background-color: rgb(255, 255, 255); line-height: 1.45;">Logic Controller</a> (i.e.,&nbsp; <a href="http://jmeter.apache.org/usermanual/component_reference.html#Simple_Controller" rel="nofollow" target="_blank" style="background-color: rgb(255, 255, 255); line-height: 1.45;">Simple Controller</a> if you just need a “container” or <a href="http://jmeter.apache.org/usermanual/component_reference.html#While_Controller" rel="nofollow" target="_blank" style="background-color: rgb(255, 255, 255); line-height: 1.45;">While Controller</a> to set a custom condition like maximum number of hits) </p>
          <p pid="37">2b. Put the HTTP Request Sampler under the Logic Controller and configure <strong style="line-height: 1.45;"> Server Name or IP</strong> and <strong style="line-height: 1.45;">Path</strong> fields to limit the extracted values to an “interesting” scope. You probably want to focus on domain(s) belonging to the application under test and don’t want it to crawl over the Internet, because if your application has any link that leads to an external resource; JMeter will go outside. Perl5-style regular expressions can be used to set the extracted links scope. </p>
          <p pid="40">
            <img alt="html link parser jmeter" class="fr-fin fr-dib" src="http://cdn2.hubspot.net/hubfs/208250/Blog_Images/spider3.png">
          </p>
          <h3>Demo</h3>
          <p pid="43">
            <img alt="html link parser demo jmeter" class="fr-fil fr-dib" src="http://cdn2.hubspot.net/hubfs/208250/Blog_Images/spider4.png">
          </p>
          <h3 pid="44">Pros</h3>
          <ul>
            <li>
              <p pid="45">Easy to configure and use.</p>
            </li>
            <li>
              <p pid="45">Acts like a “spider.”&nbsp;</p>
            </li>
          </ul>
          <h3>Cons</h3>
          <ul>
            <li>
              <p pid="48">Zero error tolerance; any failure to extract links from the response will cause cascade failures of subsequent requests.</p>
            </li>
          </ul>
          <h2>3. Advanced “Spidering” Test Plan</h2>
          <p pid="50">Assuming the limitations of the above approaches, you might want to come up with a solution that won’t break on errors and will be crawling the whole application under test. Below you can find a reference Test Plan outline which can be used as a skeleton for your “spider”:</p>
          <p pid="52">3a. Open the main page.</p>
          <ul>
            <li>
              <p pid="52">Extract all the links from it.</p>
            </li>
            <li>
              <p pid="52">Click on a random link.</p>
            </li>
            <li>
              <p pid="52">If the returned value has “good” <a href="https://en.wikipedia.org/wiki/Media_type" rel="nofollow" target="_blank" style="background-color: rgb(255, 255, 255); line-height: 1.45;">MIME type</a>&nbsp;(if link results in an image or PDF document or whatever link extraction will be skipped); extract all links from the response. </p>
            </li>
          </ul>
          <p pid="57">
            <img alt="Advanced “Spidering” Test Plan jmeter" class="fr-fin fr-dib" src="http://cdn2.hubspot.net/hubfs/208250/Blog_Images/spider5.png">
          </p>
          <p pid="58">&nbsp;Explanation of the used elements:</p>
          <ul>
            <li>
              <p pid="60">
                <strong>
                  <a href="https://www.blazemeter.com/blog/using-while-controller-jmeter?utm_source=BM&amp;utm_medium=BM_blog&amp;utm_campaign=spider-site-jmeter-tutorial" rel="nofollow" target="_blank">While Controller</a>&nbsp; </strong>is used to set the maximum amount of requests so the test won’t last forever. If you limit via scheduling, you can skip it.
              </p>
            </li>
            <li>
              <p pid="61">
                <strong>
                  <a href="http://jmeter.apache.org/usermanual/component_reference.html#Once_Only_Controller" rel="nofollow" target="_blank">Once Only Controller</a>&nbsp; </strong>is used to execute a call to the main page only once.
              </p>
            </li>
            <li>
              <p pid="62">
                <strong>
                  <a href="https://www.blazemeter.com/blog/using-xpath-extractor-jmeter-0?utm_source=BM&amp;utm_medium=BM_blog&amp;utm_campaign=spider-site-jmeter-tutorial" rel="nofollow" target="_blank">XPath Extractor</a>&nbsp; </strong>is used to filter out URLs that don’t belong to the application under test and other links that are not interesting like <strong>mailto</strong>, <strong>callto</strong>, etc. An Example XPath query will look like:
              </p>
            </li>
          </ul>
          <p pid="64" style="margin-left: 20px;">//a[starts-with(@href,'/') or starts-with(@href,'.') or contains(@href,'${SITE}') and not(contains(@href,'mailto'))]/@href</p>
          <p pid="65">Using XPath is not a must, and in some cases, it may be very memory intensive. You may need to consider other ways of fetching the links from the response. It is used for demonstration purposes as normally XPath queries are more human-readable than CSS/JQuery and especially Regular Expressions.</p>
          <ul>
            <li>
              <p pid="68">
                <strong>
                  <a href="https://guide.blazemeter.com/hc/en-us/articles/206733739-Using-JMeter-Functions-Part-II?utm_source=BM&amp;utm_medium=BM_blog&amp;utm_campaign=spider-site-jmeter-tutorial" rel="nofollow" target="_blank">__javaScript() function</a>&nbsp; </strong>actually does three things:
              </p>
              <ol>
                <li>
                  <p pid="68">Chooses&nbsp;a random link out of the extracted by the XPath Extractor.</p>
                </li>
                <li>
                  <p pid="68">Removes <strong>../</strong>&nbsp;from the beginning of the URLs. </p>
                </li>
                <li>
                  <p pid="68">Sets HTTP Request title to the current random URL.</p>
                </li>
              </ol>
            </li>
            <li>
              <p pid="68">
                <strong>
                  <a href="https://guide.blazemeter.com/hc/en-us/articles/207421325-Using-RegEx-Regular-Expression-Extractor-with-JMeter?utm_source=BM&amp;utm_medium=BM_blog&amp;utm_campaign=spider-site-jmeter-tutorial" rel="nofollow" target="_blank" style="background-color: rgb(255, 255, 255); line-height: 1.45;">Regular Expression Extractor</a>&nbsp; </strong>is used to extract Content-Type header from the response
              </p>
            </li>
            <li>
              <p pid="68">
                <strong>
                  <a href="https://www.blazemeter.com/blog/how-use-jmeters-if-controller-and-get-pie?utm_source=BM&amp;utm_medium=BM_blog&amp;utm_campaign=spider-site-jmeter-tutorial" rel="nofollow" target="_blank" style="background-color: rgb(255, 255, 255); line-height: 1.45;">If Controller</a>&nbsp; </strong>makes it so that the next round of extracting links from the response will start only if the response has matching content type.
              </p>
            </li>
          </ul>
          <h3 pid="74">&nbsp;Demo</h3>
          <p pid="76">
            <img alt="Advanced “Spidering” Test Plan jmeter demo" class="fr-fin fr-dib" src="http://cdn2.hubspot.net/hubfs/208250/Blog_Images/spider6.png">
          </p>
          <h3>Pros</h3>
          <ul>
            <li>
              <p pid="78">Extreme flexibility and configurability.&nbsp;</p>
            </li>
          </ul>
          <h3>Cons</h3>
          <ul>
            <li>
              <p pid="80">Complexity.</p>
            </li>
          </ul>
          <p pid="81">If you want to try the above scripts yourself, they are available at <a href="https://bitbucket.org/blazemeter/jmeter-spider/" rel="nofollow" target="_blank" style="background-color: rgb(255, 255, 255); line-height: 1.45;">jmeter-spider repository</a>. As usual, feel free to use the below discussion box to express any form of feedback. </p>
        </div>
      </div>
      <div id="bottom-bumper-container">
        <div id="div-gpt-ad-1435246566686-4" class="article-bumper article-bumper-bottom" data-gpt-desktop="true" data-gpt-slot="bottomBumper"></div>
      </div>
      <div class="article-tag-pill-container">
        <span class="article-tag-pill">Links</span>
        <span class="article-tag-pill">Testing</span>
        <span class="article-tag-pill">Web application</span>
      </div>
      <div class="attribution">
        <p>Published at DZone with permission of <span>Ilan Hertz</span>. <span>
            <a href="https://www.blazemeter.com/blog/how-spider-site-jmeter-tutorial" target="_blank">See the original article here. <i class="icon-link-ext-alt"></i>
            </a>
          </span>
        </p>
        <p>Opinions expressed by DZone contributors are their own.</p>
      </div>
      <div class="related">
        <h3>Popular on DZone</h3>
        <ul>
          <li class="relateddiv">
            <a href="/articles/deep-dive-into-unified-observability-exporters-met?fromrel=true">Unified Observability Exporters: Metrics, Logs, and Tracing</a>
          </li>
          <li class="relateddiv">
            <a href="/articles/event-driven-order-processing-program?fromrel=true">Event-Driven Order Processing Program</a>
          </li>
          <li class="relateddiv">
            <a href="/articles/now-its-time-to-uncomplicate-with-the-not-so-new-a?fromrel=true">Now It's Time to Uncomplicate With the Not-So-New API in Java</a>
          </li>
          <li class="relateddiv">
            <a href="/articles/what-is-selenium-getting-started-with-selenium-aut?fromrel=true">What Is Selenium? Getting Started With Selenium Automation Testing</a>
          </li>
        </ul>
      </div>
      <div class="comments-overlay"></div>
      <div id="comment-box">
        <div class="comment-box-wrapper">
          <div id="comment-input-editor"></div>
        </div>
        <div class="info hidden"></div>
        <div class="comments-content">
          <div class="comment-header">
            <hr>
            <span class="icon-comment">
              <span class="numOfComments"></span> Comments </span>
          </div>
          <div class="comments"></div>
        </div>
      </div>
    </div>
</article>
