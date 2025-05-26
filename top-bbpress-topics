<?php
/**
 * Plugin Name: Top bbPress Topics Shortcode
 * Description: Displays popular bbPress topics by reply count, optionally limited by forum. Usage: [top_bbpress_topics topics=3 forums=23,45]
 * Version: 1.0
 * Author: Vestra Interactive
 */

if (!defined('ABSPATH')) exit;

add_action('init', function () {
    add_shortcode('top_bbpress_topics', 'vestra_top_bbpress_topics_shortcode');
});

function vestra_top_bbpress_topics_shortcode($atts) {
    if (!function_exists('bbp_get_topic_reply_count') || !post_type_exists('topic')) {
        return '<p>тЪая╕П bbPress is not available or not fully initialized.</p>';
    }

    $atts = shortcode_atts(array(
        'topics' => 3,
        'forums' => '',
    ), $atts, 'top_bbpress_topics');

    $forum_ids = array_filter(array_map('absint', explode(',', $atts['forums'])));

    $query_args = array(
        'post_type'      => 'topic',
        'posts_per_page' => intval($atts['topics']),
        'post_status'    => 'publish',
        'meta_key'       => '_bbp_reply_count',
        'orderby'        => 'meta_value_num',
        'order'          => 'DESC',
        'no_found_rows'  => true,
    );

    if (!empty($forum_ids)) {
        $query_args['post_parent__in'] = $forum_ids;
    }

    $topics = new WP_Query($query_args);

    if (!$topics->have_posts()) {
        $query_args['orderby'] = 'date';
        unset($query_args['meta_key']);
        $topics = new WP_Query($query_args);
        $output = '<ul class="bbpress-topics"><li class="fallback-notice">Showing latest topics (no replies yet)</li>';
    } else {
        $output = '<ul class="bbpress-topics">';
    }

    if ($topics->have_posts()) {
        while ($topics->have_posts()) {
            $topics->the_post();
            $post_id     = get_the_ID();
            $title       = get_the_title($post_id);
            $link        = get_permalink($post_id);
            $author_id   = get_the_author_meta('ID');
            $author_name = get_the_author();
            $avatar      = get_avatar($author_id, 24, '', '', ['class' => 'topic-avatar']);
            $freshness   = human_time_diff(get_the_time('U'), current_time('timestamp')) . ' ago';
            $reply_count = bbp_get_topic_reply_count($post_id);
            $reply_text  = $reply_count === 0 ? 'No replies yet' : sprintf('%d replies', intval($reply_count));

            $output .= '<li>
              <span class="topic-icon">ЁЯЧия╕П</span>
              <div class="topic-meta">
                <a class="topic-title" href="' . esc_url($link) . '">' . esc_html($title) . '</a>
                <div class="topic-details">
                  <span class="reply-count">' . esc_html($reply_text) . '</span> тАв 
                  <span class="avatar">' . $avatar . '</span>
                  <span class="author">' . esc_html($author_name) . '</span> тАв 
                  <span class="freshness">' . esc_html($freshness) . '</span>
                </div>
              </div>
            </li>';
        }
    } else {
        $output .= '<li>No topics found.</li>';
    }

    wp_reset_postdata();
    $output .= '</ul>';

    return $output;
}
