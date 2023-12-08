<?php 

add_action('init', 'create_course_expiration_field');

function create_course_expiration_field() {
  register_meta('user', 'course_expiration_date', array(
    'type' => 'string',
    'single' => true,
    'show_in_admin_table' => false
  ));
}

add_action('user_register', 'assign_bonus_course_on_registration', 10, 1);
function assign_bonus_course_on_registration($user_id) {

	$expiration_date = date('Y-m-d', strtotime('+30 days'));
	update_user_meta($user_id, 'course_expiration_date', $expiration_date);

    $course_id = 4126;
    learndash_set_users_for_course($course_id ,  array( $user_id ) );

}

function check_for_expired_courses() {
	$users = get_users(array(
	  'meta_key' => 'course_expiration_date',
	  'meta_compare' => '<',
	  'meta_value' => date('Y-m-d')
	));
  
	if ($users) {
	  foreach ($users as $user) {
		$course_id = 4126; // Replace with your course ID
		learndash_remove_user_from_course($course_id, $user->ID);

		// Delete user's course and quiz data (optional)
		learndash_delete_user_course_progress($user->ID, $course_id);
		learndash_delete_user_quiz_results($user->ID, $course_id);
	  }
	}
  }
  
  add_action('wp_schedule_event', 'schedule_course_expiration_check');
  
  function schedule_course_expiration_check() {
	if (!wp_next_scheduled('course_expiration_check_event')) {
	  wp_schedule_event(time(), 'daily', 'course_expiration_check_event');
	}
  }
  
  add_action('wp_schedule_event', 'check_for_expired_courses');

?>
