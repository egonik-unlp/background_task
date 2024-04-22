pub async fn handle_trigger(
  Extension(task_handle): Extension<crate::MyActorHandle>,
  extract::Json(payload): extract::Json<PostTrigger>,
) -> Result<Response, Error> {
  tracing::info!(
      "Starting to run Crawler at: {}",
      crate::models::get_utc_timestamp_as_rfc3339()
  );

  let masterfile_key = match payload.object.key {
      Some(value) => {
          if value.is_empty() {
              return Err(RequestNotSuccessful::new(
                  StatusCode::BAD_REQUEST,
                  "AWS IoT Analytics S3 masterfile key in POST request is blank!",
              )
              .into());
          }

          value
      }
      None => {
          return Err(RequestNotSuccessful::new(
              StatusCode::BAD_REQUEST,
              "AWS IoT Analytics S3 masterfile key missing in POST request!",
          )
          .into())
      }
  };

  task_handle.run_task(&masterfile_key).await;

  tracing::info!("POST /v1/trigger request handler completed");
  Ok(Json(json!({ "status": "success" })).into_response())
}




https://www.reddit.com/r/rust/comments/fddf6y/handling_longrunning_background_tasks_in_actixweb/




https://rust-lang.github.io/async-book/06_multiple_futures/04_spawning.html


https://desilva.io/posts/spawn-tasks-and-talk-to-them-via-a-channel-with-axum




