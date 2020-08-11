# WooCommerce-Auto-Category-Product-Thumbnails
//This code returns the product image to used as the category thumnail


//get child categories 

function getChildCategories(){
    
        $categories = get_categories(
        array(
            'hide_empty' =>  0,
            'taxonomy'   =>  'product_cat', 
            'parent'     => 35
            )
        );
        
        //print_r($categories);
        
        foreach($categories as $category) { 
           
            $args = array(
            'posts_per_page' => -1,
            'tax_query' => array(
                'relation' => 'AND',
                array(
                    'taxonomy' => 'product_cat',
                    'field' => 'slug',
                    // 'terms' => 'white-wines'
                    'terms' => $category->slug
                )
            ),
            'post_type' => 'product',
            'orderby' => 'title,'
        );
            
            
            $category_id = $category->term_id;
            $category_name = $category->name;
            $products = new WP_Query($args);
            
            //print_r($products);
            
            while ( $products->have_posts() ) {
            $products->the_post();
                
                $postid = $products->post->ID;
                $product_meta = get_post_meta($postid);
                
                if(get_the_post_thumbnail_url($postid)){
                    
                    $img_url = get_the_post_thumbnail_url($postid);
                    
                    $asd = explode('uploads/', $img_url);

                    global $wpdb;
                    
                     $table_name = 'shop_posts';
                     $wpdb->insert(
                                    $table_name,
                                    array(
                                            'post_author'=> 1,
                                            'post_date'=> '2020-08-10 12:18:34',
                                            'post_date_gmt'=> '2020-08-10 12:18:34',
                                            'post_content'=>'',
                                            'post_title'=>$category_name,
                                            'post_excerpt'=>'',
                                            'post_status'=>'inherit',
                                            'comment_status'=>'open',
                                            'ping_status'=>'closed',
                                            'post_password'=>'',
                                            'post_name'=>$category_name,
                                            'to_ping'=>'',
                                            'pinged'=>'',
                                            'post_modified'=>'2020-08-10 12:18:34',
                                            'post_modified_gmt'=>'2020-08-10 12:18:34',
                                            'post_content_filtered'=>'',
                                            'post_parent'=>0,
                                            'guid'=>$img_url,
                                            'menu_order'=>0,
                                            'post_type'=>'attachment',
                                            'post_mime_type'=>'image/jpeg',
                                            'comment_count'=>0),
                                    array( '%d','%s','%s','%s','%s','%s','%s','%s','%s','%s','%s','%s','%s','%s','%s','%s', '%d','%s','%d','%s','%s','%d')
                                 );
                    
                     $insertId = $wpdb->insert_id;
                     //echo $insertId."<br/>";
                     
                     $table_name1 = 'shop_postmeta';
                     $metvalue = $asd[1];
                     
                     $wpdb->insert(
                                    $table_name1,
                                    array(
                                            'post_id'=> $insertId,
                                            'meta_key'=> '_wp_attached_file',
                                            'meta_value'=>  $metvalue   ),
                                    array( '%d','%s','%s')
                                 );
                     
                    
                    $insert2 = $wpdb->query("INSERT INTO `shop_termmeta`( `term_id`, `meta_key`, `meta_value`) VALUES ($category_id,'thumbnail_id',$insertId)");
                    break;
                
                }
              ?>  
            <?php
          }     
       }    
    }
add_shortcode( 'display_categories', 'getChildCategories' );

