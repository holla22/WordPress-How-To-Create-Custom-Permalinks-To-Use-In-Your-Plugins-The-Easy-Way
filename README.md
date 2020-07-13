# WordPress: How To Create Custom Permalinks To Use In Your Plugins The Easy Way

## This is an old tutorial I've written back in 2012 and not on my website anymore. Saving it here because a lot of people found it useful.

<a class="bmc-button" target="_blank" href="https://www.buymeacoffee.com/z33man"><img src="https://cdn.buymeacoffee.com/buttons/default-orange.png" alt="Buy Me A Coffee" height="41" width="174"></a>
  
Hi guys, in this tutorial I will be teaching you how you can create custom permalinks. We are going to write a simple plugin to demonstrate how to do this.

The first thing to do is add to filters and one action for our functions that we will be creating.

    add_action( 'wp_loaded','my_flush_rules' );
    add_filter( 'rewrite_rules_array','my_insert_rewrite_rules' );
    add_filter( 'query_vars','my_insert_query_vars' );

The only action we use is the wp_loaded action for our function called my_flush_rules.

The first filter will be for our function called my_insert_rewrite_rules which will add new rules to our rules options in the database.

The second filter is our query_vars filter for our function called my_insert_query_vars.

**Our first function is to add new rules to the database and flush the permalinks if the rules are not set.**

    function my_flush_rules() {
        $rules = get_option( 'rewrite_rules' );
    
        if ( ! isset( $rules['(teaching-you)/(.+)$'] ) ) {
            global $wp_rewrite;
            $wp_rewrite->flush_rules();
        }
    
    }

In the function above we have set our new rules to be able to handle url’s like http://yourdomain.com/teaching-you/whatever-name-you-want-here where the “whatever-name-you-want-here” part will be the value you are passing on like a $_GET variable. You will see in the “$rules['(teaching-you)/(.+)$']” part we use regex to handle any name after teaching-you.

**Here we create our rules URL to be handled when it comes from the browser.**

    function my_insert_rewrite_rules( $rules ) {
        $newrules = array();
        $newrules['(teaching-you)/(.+)$'] = 'index.php?pagename=$matches[1]&friendly=$matches[2]';
        
        return $newrules + $rules;
    }

The above function adds the URL type to be handled when the browser sends data to the page. We have two $matches that we target. The first one is for our page name which which is the slug “teaching-you”. The second $matches we use as our $_GET value that we need to capture.

**Adding our friendly id so that WordPress recognizes it.**

    function my_insert_query_vars( $vars ) {
    
        array_push($vars, 'friendly');
        
        return $vars;
    }

In the above function we add an array_push method to add all our pretty permalink variables  for WordPress to recognize and use.

**Add a custom function to call our new custom permalink structure**

    function custom_permalink() {
    
        $friendlyurl = get_query_var( 'friendly' );
        
        if( isset($friendlyurl) ) {
        
            echo '<h2>Yay! My friendly url seems to work, here is the $friendlyurl value: '.$friendlyurl.'</h2>';
        
        }else{
        
            echo "<h2>Aaah! No friendly url here something is wrong!</h2>";
        
        }
    }

    add_shortcode('permalink','custom_permalink');

    
In the function above we are calling our $_GET using the get_query_var method as a normal $_GET won’t work so we have to do it this way to get our value from the browser.

Next we check if our variable $friendlyurl has a value set, if it does we echo a message to see that it’s working.

If it does not work add a message telling us that it’s not set.

The last thing we do is add a shortcode to make things a bit easier for us to setup.

Now that our code is done

Our code is done, now we need to create a page called Teaching You with a permalink slug “teaching-you”

Next add our shortcode to the page “[permalink]” and click publish. Before you view your page you need make sure that you have set the correct permalink structure under Settings->Permalinks to Post name

Now you can view your page, go to your page you created called http://yourdomain.com/teaching-you and you will get a message telling you that your value is not set.

If you type a name or number after the teaching-you part like so http://yourdomain.com/teaching-you/your-name-or-number-here You will get a totally different message telling you that it worked and gives the value you have entered in the url.

That’s it hope you liked this tutorial, PLEASE COMMENT and ASK QUESTIONS if you don’t understand something.
