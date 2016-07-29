fastlane_version "1.55.0"

default_platform :ios

  before_all do
  end

  lane :test do
    setup_and_run_tests()
  end

  lane :hockey do
    setup_and_run_tests()
    deploy_hockey()
  end

  lane :hockey_no_test do
    setup()
    deploy_hockey()
  end
 
  def setup_and_run_tests()
    setup()
    scan
  end

  def deploy_hockey()
    icon_overlay(version: get_version_number)
    set_build_number()
    update_info_plist
    build_with_gym()
    upload_to_hockeyapp()
  end

  def build_with_gym()
    if ENV['TAB_USE_LEGACY_BUILD_API']
      gym(use_legacy_build_api: true)
    else
      gym
    end
  end

  def upload_to_hockeyapp()
    custom_notes = ENV['CUSTOM_HOCKEY_RELEASE_NOTES'] || ""
    notes = custom_notes == "" ? create_change_log() : custom_notes
    hockey(notes_type: "0", notes: notes)
  end

  def setup()
    ENV['SCAN_SCHEME'] = ENV['GYM_SCHEME']
    if ENV['SCAN_DEVICE'] == nil
      ENV['SCAN_DEVICE'] = "iPhone 6 (9.3)"
    end
    if is_ci && ENV['XCODE_PATH'] != nil
      xcode_select(ENV['XCODE_PATH'])
    end
  end

  def set_build_number()
    use_timestamp = ENV['TAB_USE_TIME_FOR_BUILD_NUMBER'] || false
    use_ci_build_number = ENV['TAB_CI_BUILD_NUMBER'] || false
    if use_timestamp
      increment_build_number(build_number: Time.now.strftime("%y%m%d%H%M"))
    elsif use_ci_build_number
      increment_build_number(build_number: ENV['BUILD_NUMBER'])
    else
      increment_build_number()
    end
  end

  def create_change_log()
    cmd = "git log --after={1.day.ago} --pretty=format:'%an%x09%h%x09%cd%x09%s' --date=relative"
    output = `#{cmd}`
    return (output.length == 0) ? "No Changes" : output
  end

  after_all do |lane|
  end

  error do |lane, exception|
  end
