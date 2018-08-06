## TYPE 1:
  * messages

## TYPE 2:
label_errors => true
inline_errors => true

## TYPE 3:
starts in javascript, ends in javascript

## TYPE 4:
* stripe / create group / payment form
*  SUCCESS AND NOTICE DISPLAY
* assets / javascripts / manage
* assets / javascripts / create_groups
* controllers / users / registrations
* controllers / media_items
* controllers / accounts
* controllers / groups
* controllers / images
* controllers / writings
* views / browse / index
* views / manage / manage families
* views / manage / manage items
* views / manage / manage accounts

ERROR DISPLAY
flash-messages
flash_error
flash_alert
flash_notice (success)
flash_guidance (user guidelines)

phrasing of error/success messages


==============================================


TYPE 1:

messages


theglassfiles_com/app/assets/stylesheets/application.css.scss

// message, notice, error display

#flash_success {
  margin: 70px 15px 0 15px;
  text-align: center;
  background-color: $green-dark_color;
  color: $white_color;
  font-size: 30px;
}

#flash_notice {
  margin: 70px 0 0 0;
  text-align: center;
  background-color: $green-dark_color;
  color: $white_color;
  font-size: 30px;
}

#flash_alert {
  margin: 70px 0 0 0;
  text-align: center;
  color: red;
  font-size: 30px;
}


==============================================


##TYPE 2:

label_errors => true
inline_errors => true


* validation errors
* theglassfiles_com/config/locales/en.yml

* en:
  * activerecord:
    * errors:
      * models:
        * user:
          * attributes:
            * name_first:
              * blank: "is required "
  * email:
   * blank: "is required "
   * invalid: "is not valid"
  * password:
   * blank: "is required "
  * password_confirmation:
   * blank: "is required "


devise errors
theglassfiles_com/config/locales/devise.en.yml


theglassfiles_com/app/views/devise/registrations/new.html.haml

 = bootstrap_form_for(resource,
                     :as => resource_name,
                     :url => registration_path(resource_name),
                     :html => { :class => 'form-vertical' },
                     :label_errors => true) do |f| 


theglassfiles_com/app/controllers/users/registrations_controller.rb

def update_email
  
  if @user.errors.any?
      
      flash[:notice] = 'You updated your email successfully.'
      redirect_to change_email_path
    else
      flash[:notice] = 'Verify your information and try again.'
      render 'edit_email'
      #redirect_to profile_path
      #redirect_to change_email_path
    
  end
  
end

def update_password

  if @user.errors.any?
      
      flash[:notice] = 'Verify your information and try again.'
      redirect_to change_email_path
    else
      flash[:notice] = 'You updated your password successfully.'
      render :edit_email

  end
end


theglassfiles_com/app/models/user.rb

validates_presence_of :name_first
#                     :password_confirmation
#                     :subdomain


theglassfiles_com/app/views/devise/sessions/new.html.haml

= bootstrap_form_for(resource,
                    :as => resource_name,
                    :url => session_path(resource_name),
                    :html => {:class => 'form-vertical' },
                    :inline_errors => true) do |f|

    %h3                              
        = render 'layouts/messages' 
        = devise_error_messages! 
                                                               
    = f.email_field :email, autofocus: true, label: "Email address"
    
    = f.password_field :password
    
    /= f.error_summary
    
    /= f.check_box :remember_me                    
    
    = f.content_tag :div, "", class: 'container_button' do 
        = f.submit 'Enter', class: "btn btn-md btn-primary"
        = f.content_tag :div, raw("If you've lost your password, you can #{ link_to "reset your password here",  new_password_path(resource_name), class: 'turn_bold' }."), class: 'button_disclaimer'

    /= @user.errors.full_messages                


==============================================


TYPE 3:

starts in javascript, ends in javascript


theglassfiles_com/app/assets/javascripts/create_images.js

  function display_error(label_for, error_text) {
    var label = $("[for='"+label_for+"']");
    var error_message = label.text().split(" - ")[0] + " - " + error_text;

    label.parent().addClass('has-error');
    label.text(error_message);

    scrollTo(label);  
  }

  //form front-end validation
  function validate_form(){
    var invalid = []
    
    invalid[0] = validate_file_presence();
    invalid[1] = validate_description();
    // invalid[2] = validate_input_file_type();  
    // invalid[3] = validate_input_file_size();

    return invalid;
  }

  function validate_description() {
    if ($('#image_description').val() === '' ){
      display_error(LABEL_DESCRIPTION, ERROR_DESCRIPTION_REQUIRED);    
      return false;
    }
    clear_error(LABEL_DESCRIPTION);
    return true;
  }

  $('#image_description').parent().focusout(function (){
    if (validate_description()){
      clear_file_input(original_file_input);
      clear_error(LABEL_ORIGINAL)
      $('#image_description').parent().removeClass('has-error');    
    }


==============================================


TYPE 4:

stripe / create group / payment form


theglassfiles_com/app/views/groups/new.html.haml


= bootstrap_nested_form_for(@account, url: groups_path(@group), id: 'new_account') do |f|        
  
  = f.fields_for :group, Group.new do |g|
    = g.text_field :name, :required => true, label: "Family name"
   
    = g.text_field :town, :required => true, label: "Town of Family origin", help: raw("A place that is very important to your family history. This could be a place of family gathering, or a<br> town where many family members live or are originally from.<br> <span class='turn_italic'>Examples:</span><br><span class='turn_bold'>Springfield, MA, USA</span><br><span class='turn_bold'>Timbuktu, Mali</span>")          
  = f.content_tag :div, class: 'form-group' do
    = image_tag("icons_payment_methods.png", style: "width: 690px;") 


#flash-messages


theglassfiles_com/app/assets/javascripts/form.js


line 61:

show_error = function (message) {
  $("#flash-messages").html('<div class="alert-alert"><a data-dismiss="alert">×</a><div id="flash_alert">' + 'There is a problem. Please check entries' + '</div></div>');
  
   //document.getElementById("form_description").style.color = "blue";

   document.getElementById("credit_card_number").style.color = "#ff0000";
   document.getElementsByTagName("label").style.color = "#ff0000";
   document.getElementById("credit_card_name").style.color = "#ff0000";
   document.getElementById("credit_card_zip").style.color = "#ff0000";
   document.getElementById("credit_card_exp_month").style.color = "#ff0000";
   document.getElementById("credit_card_exp_year").style.color = "#ff0000";
   document.getElementById("credit_card_code").style.color = "#ff0000";
   
  return false;
};


line 140:

show_error = function (message) {
  $("#flash-messages").html('<div class="alert-alert"><a data-dismiss="alert">×</a><div id="flash_alert">' + message + '</div></div>');
  return false;
};


==============================================

SUCCESS AND NOTICE DISPLAY

==============================================


to render messages

The partial
  app/views/layouts/_messages.html.haml
can be used to display any flash messages you create.

.container
    .row
        .col-xs-12
             = render 'layouts/messages'

This partial appears below the header.

It calls
  app/views/layouts/_messages.erb

<% flash.each do |key, value| %>
  <div class="alert alert-<%= key %>">
    <a href="#" data-dismiss="alert" class="close">×</a>
    <ul>
      <li>
        <%= value %>
      </li>
    </ul>
  </div>
<%# clears the error so it doesn't appear on every subsequent page you go to %>
  <% flash.clear%>
<% end %>

You can use
  render 'layouts/_messages'
anywhere in your view to display messages.

In some cases, it is possible that there will be two flash messages displayed on the same page. In such cases, the display hierarchy is:
1- the result of the action you just took
2- the special case you find yourself in


=============================


assets / javascripts / manage


var ERROR_EMAIL_INVALID       = "Email address is not a valid email address";


// manage families view members 
function handle_role_click(role_button, group_id, member_id, role) {
        
    if ($(role_button).hasClass("js_member")) 
    { 
      grant_role_ajax(role_button, group_id, member_id, role);
    }
    else {      
      
      var r = confirm("Are you sure you want to proceed?");
      if (r == true) {
        revoke_role_ajax(role_button, group_id, member_id, role);
      } 
      else {
        console.log("cancel")
      }       
    }
}


function handle_remove_from_family_click(member_id, html_url) {  

  var r = confirm("Are you sure you want to proceed?");
  if (r == true) {
    remove_from_family_ajax(member_id, html_url)
  } 
  else {
    console.log("cancel")
  }   

}


// handle leave family
function handle_leave_family(leave_action, url, member_id, group_name) {
  var r = confirm("Are you sure you want to leave this Family?");
  if (r == true) {
    leave_family_ajax(leave_action, url, member_id, group_name)
  } 
  else {
    console.log("cancel")
  } 

}

function leave_family_ajax(leave_action, url, member_id, group_name){
    notice = "You+have+left+the+"+group_name+"+Family."

    link_dirty = window.location.href
    split_link = link_dirty.split('/')
    link = split_link[0] + "//" + split_link[2]
    link += url + "?" + "member_id=" + member_id + "&" + "notice=" + notice

    window.location = link
}


// delete item 
function handle_delete_item(item_id, html_url)
{

  var r = confirm("Are you sure you want to delete this media item?");
  if (r == true) {
    delete_item_ajax(item_id, html_url)
  } 
  else {
    console.log("cancel")
  } 

}


=============================


assets / javascripts / create_groups

  show_error = function (message) {
    $("#flash-messages").html('<div id="flash_alert" style="margin-bottom: 60px;">' + 'All entries must be accurate.' + '</div>');


=============================


controllers / users / registrations

update_email
      flash[:success] = 'Your email address was changed successfully.'

update_password
      flash[:success] = 'Your password was changed successfully.'


=============================


controllers / media_items

create
      flash[:success] = 'Your media item is now saved in your library.'

update
      flash[:success] = 'You successfully updated the details of your media item.'

destroy
      flash[:success] = 'You have deleted the media item you selected.'


=============================


controllers / accounts

create
      flash[:success] = 'You created your Family account successfully!'

update
      flash[:success] = 'You updated your Family account successfully.'

destroy
      flash[:success] = 'You deleted a Family account. Would you like to create a new one?'

update_payment_card
      flash[:success] = 'Your updated credit card information is approved.'

update_user_password
      flash[:success] = 'Your password was changed successfully.'

update_user_email
      flash[:success] = 'Your email address was changed successfully.'

update_group_name_town
      flash[:success] = 'You changed your Family account information successfully.'

update_user_name_town
      flash[:success] = 'You changed your Personal account information successfully.'


=============================


controllers / groups


grant_group_role

      if group.add(member, as: role)
        format.html {render :nothing => true, :notice => 'We sent a notification of your request to #{member.name_first}.'}
      else  
        format.html {render nothing: true, notice: "We were not able to fulfill your request. Please notify us of this issue by emailing customerservice@theglassfiles.com"}
      end


revoke_group_role

      if group.users.delete(member, as: role) 
        format.html {render :nothing => true, :notice => 'We sent a notification of your action to #{member.name_first}.'}
      else  
        format.html {render nothing: true, notice: "We were not able to fulfill your request. Please notify us of this issue by emailing customerservice@theglassfiles.com"}
      end


remove_group_user

      if group.users.delete(member)

        if member == current_user
          flash[:success] = "You have left the #{group.name} Family."
        else
          flash[:success] = "You removed #{member.name_first} from the #{group.name} Family."
        end

        format.html {redirect_to "/manage/families", notice: "You removed #{member.name_first} from the #{group.name} Family."}        
      else
        format.html {redirect_to "/manage/families", alert: "We were not able to fulfill your request. Please notify us of this issue by emailing customerservice@theglassfiles.com"}
      end


create

    respond_to do |format|      
      url = manage_families_dynamic_url(@account.group.id, "invite_members", "invitemembers")
      flash[:success] = "You created your Family group successfully! Now <a href='#{url}'>invite members to it</a>."

      format.html {redirect_to manage_families_dynamic_path(@account.group.id, "invite_members", ""), success: 'You created your Family group successfully.'}
    end


manage_families

    if @form == "view_members"      
      if (@curr_group.users(as: :member) +  @curr_group.users(as: :manager) +  @curr_group.users(as: :admin)).count > 1
        @members = @curr_group.get_group_members(current_user)
      else         
        respond_to do |format|
          url = manage_families_dynamic_url(@curr_group.id, "invite_members", "invitemembers")
          flash[:success] = "You don't have any family members yet <a href='#{url}'>Invite members now</a>!"
          
          redirect_to manage_families_dynamic_path(@curr_group.id, "invite_members", "")
          return(0)
        end        
      end
    elsif @form == "invite_members"


=============================


controllers / browse


tag

      flash[:notice] = "There are no items with the tag you selected."


=============================


controllers / images


create
      flash[:success] = 'Your media item is now saved in your library.'


update
      flash[:success] = 'You successfully updated the details of your media item.'


historian_update_sharings

      if in_group && !@image.in_group?(group)
        if gid == Group::WORLD_ID || gid == Group::COMMUNITY_ID
          format.html { render :nothing => true, :notice => "The item you selected is no longer shared with the #{group.name} group." }
        else
          flash[:success] = "The item you selected is no longer shared with the #{group.name} group."
          format.html { redirect_to manage_path_group(gid, "#browse"), success: 'The item you selected is no longer shared with the #{group.name} group.' }
        end
      elsif !in_group && @image.in_group?(group)
        format.html { render :nothing => true, :notice => "The item you selected is now shared with the #{group.name} group. Nice!" }
      else
        flash[:alert] = "We were not able to fulfill your request. Please notify us of this issue by emailing <a href='mailto:customerservice@theglassfiles.com'>customerservice@theglassfiles.com</a>"
        format.html { render nothing: true }
      end


destroy

    if @image.destroy then
      respond_to do |format|
        flash[:success] = 'You have deleted the media item you selected.'
        format.html { redirect_to images_url, success: 'You have deleted the media item you selected.' }
        format.json { head :no_content }
      end
    else
      respond_to do |format|
        format.html { redirect_to images_url, alert: 'You do not have permission to delete this media item.' }
        format.json { head :no_content }
      end
    end


=============================


controllers / writings


create
      flash[:success] = 'Your media item is now saved in your library.'


update
      flash[:success] = 'You successfully updated the details of your media item.'


historian_update_sharings

      if in_group && !@writing.in_group?(group)
        if gid == Group::WORLD_ID || gid == Group::COMMUNITY_ID
          format.html { render :nothing => true, :notice => "The item you selected is no longer shared with the #{group.name} group." }
        else
          flash[:success] = "The item you selected is no longer shared with the #{group.name} group."
          format.html { redirect_to manage_path_group(gid, "#browse"), success: 'The item you selected is no longer shared with the #{group.name} group.' }
        end
      elsif !in_group && @writing.in_group?(group)
        format.html { render :nothing => true, :notice => "The item you selected is now shared with the #{group.name} group. Nice!" }
      else
        flash[:alert] = "We were not able to fulfill your request. Please notify us of this issue by emailing <a href='mailto:customerservice@theglassfiles.com'>customerservice@theglassfiles.com</a>"
        format.html { render nothing: true }
      end


destroy

    @writing.destroy
    respond_to do |format|
      flash[:success] = 'You have deleted the media item you selected.'
      format.html { redirect_to writings_url, success: 'You have deleted the media item you selected.' }
      format.json { head :no_content }
    end


=============================


views / browse / index

.flash_message

#flash_notice
You haven't created any media items yet. <a href="#{create_items_landing_url}">Create your first item now</a>!

#flash_notice
No one has shared items with this family yet. <a href="#{manage_items_group_id_path(0)}">Share your first item now</a>!

#flash_guidance
Click or tap a circle to see items shared by members of that group.


=============================


views / browse / tag

.container
    .flash_message{:id => "flash_notice"}
        These items have the '#{@tag_name}' tag.


=============================


views / manage / manage families

.flash_message

#flash_notice
You have not yet created or joined families. <a href="#{create_families_url}">Create a family today</a>!

#flash_guidance
Each circle represents a group you're a part of.


=============================


views / manage / manage items

.flash_message

#flash_notice
You haven't created any media items yet. <a href="#{create_items_landing_url}">Create your first item now</a>!

#flash_guidance
Click or tap a circle to see items you can manage in that group.


=============================


views / manage / manage accounts

.flash_message

#flash_guidance
Administer your personal account and the groups you've created.


==============================================


----

ERROR DISPLAY

----

flash-messages

flash_error

flash_alert

flash_notice (success)

flash_guidance (user guidelines)

----

views with messages displayed by rendering 'layouts/display_messages' 
  devise/sessions/new.html.haml
  devise/registrations/edit_email.html.haml
  devise/registrations/edit_password.html.haml

----

groups/new
  
Within
groups/new
is a Stripe form to create an account.
  
This form's error messages are controlled by Stripe javascript.

----

For the following forms, some error messages are generated by a javascript file and others are called by the controllers.
  
  media_items/new
  media_items/update
  media_items/destroy
  
  slideshows/new
  slideshows/update
  slideshows/destroy
  
  videos/new
  videos_items/update
  videos_items/destroy
  
  writings/new
  writings/update
  writings/destroy

----

Views with rails-generated errors on the form, which can be overridden by en.yml
  devise/passwords/new.html.haml
  devise/passwords/edit.html.haml
  devise/registrations/edit.html.haml

----

not logged in:

register
app/views/devise/registrations/new.html.haml

reset password + reset password url (2 step process)
app/views/devise/passwords/new.html.haml
app/views/devise/passwords/edit.html.haml

login
app/views/devise/sessions/new.html.haml

----

logged in:

payment (create group/family)
TODO

change password
app/views/devise/registrations/_edit_password.html.haml

change email address
app/views/devise/registrations/_edit_email.html.haml


==============================================


phrasing of error/success messages

rails validation (register)

http://guides.rubyonrails.org/i18n.html


----


{ legacy NYCNAK }

phrasing of error messages

simpleform validation

config/locales/simple_form.en.yml


----
