# LiveProject
Live project 2 week sprint completed through the Tech Academy

## Introduction
The final two weeks of coursework at the Tech Academy was a two week sprint working on an ASP.NET MVC C# project with a code first Entity Framework Database. Having the opportunity to work on a project in the middle of its lifecycle, I was able to see how other developers on the project were able to solve problems, create features, and fit all the different pieces of the project together. I worked on some [back end stories](#back-end-stories) and a few [front end stories](#front-end-stories) that utilized different skillsets learned throughout the boot camp. After completion of this project sprint, I'm even more eager to start working on large projects on a bigger scale professionally.
  
Here are some of the stories I worked on during the two week sprint:


## Back End Stories
* [Create BlogPhoto Model and CRUD Pages](#create-blogphoto-model-and-crud-pages)
* [Photo Storage & Retrieval](#photo-storage-and-retrieval)



### Create BlogPhoto Model and CRUD Pages
The blog portion of the site required a way to enter and display photos. For this, I needed to create a model within the project specific to the blog photos as well as CRUD (Create, Read, Update, Delete) pages associated with the photos.
The creation of the model with the requested properties:

        using System;
        using System.Collections.Generic;
        using System.Linq;
        using System.Web;

        namespace TheatreCMS3.Areas.Blog.Models
        {
            public class BlogPhoto
            {
                public int BlogPhotoId { get; set; }
                public string Title { get; set; }
                public byte[] Photo { get; set; }
            }
        }
       
 Using NuGet Package Manager Console, I updated the databse to create the table with these properties, and because this program had automatic migrations enabled, no migration was necessary at this time.
 Within the Controllers, I then added a new MVC5 Controller with views using Entity Framework, including a layout page. This successfully created the CRUD pages needed for the photos section of this project.
 
 ### Photo Storage and Retrieval
This task asked to make it possible for the user to be able to add photos of their choice to the page, and they needd to be stored in the databse. Based on requirements, the photos were to be stored as a byte[] (byte array).
Created a method within the Blog Photo section Controller:

    public byte[] convertImage(HttpPostedFileBase postedFile)
        {
            byte[] bytes;
            using (BinaryReader br = new BinaryReader(postedFile.InputStream))
            {
                bytes = br.ReadBytes(postedFile.ContentLength);
            }
            return bytes;
        }

I then updated the Create method that was created when the files were scaffolded to be able to take in the postedFile object within the Create method:

       public ActionResult Create([Bind(Include = "BlogPhotoId.Title.Photo")]
       BlogPhoto blogPhoto, HttpPostedFileBase postedFile
       
I then needed to be able to convert the uploaded file:

        [HttpPost]
        [ValidateAntiForgeryToken]
        public ActionResult Create([Bind(Include = "BlogPhotoId,Title,Photo")] BlogPhoto blogPhoto, HttpPostedFileBase postedFile)
        {
            if (ModelState.IsValid)
            {
                blogPhoto.Photo = convertImage(postedFile);
                db.BlogPhotos.Add(blogPhoto);
                db.SaveChanges();
                return RedirectToAction("Index");
            }

            return View(blogPhoto);
        }
        
I added an upload image function within the create page portion of the controller:

        <div class="form-group">
                    @Html.LabelFor(model => model.Photo, htmlAttributes: new { @class = "control-label col-md-2" })
                    <div class="col-md-10">
                        <input type="file" id="uploadImage" name="postedFile" onchange="PreviewImage();" required />
                        @Html.ValidationMessageFor(model => model.Photo, "", new { @class = "text-danger" })
                    </div>
                </div>
                
Once the photo is then uploaded and stored, I had to create a way of retrieving the photo using the byte[] to display in the photo Index page:

      @foreach (var item in Model)
      {
              <div class="BlogPhoto-Index--Flexbox">
                  <div class="row row-cols-2 g-2">
                      <div class="BlogPhoto-Index--col">
                          <div class="BlogPhoto-Index--Overlay">
                              <div class="BlogPhoto-Index--card">

                                  @if (item.Photo != null)
                                  {
                                      <img src="data:image/jpg;base64,@(Convert.ToBase64String(item.Photo))" style="width: 300px; height: 200px;" />

                                  }
                                  else
                                  {
                                      <img src="~/Content/images/BlogPostRobot.jpg" style="width: 300px; height: 200px;" />
                                  }
                                  <div class="BlogPhoto-Index--card-body">

                                      <h2 class="card-title">@(item.Title)</h2>
                                      <a href="~/Areas/Blog/Views/BlogPhotoes/Edit.cshtml" class="badge badge-dark">Edit</a>
                                      <a href="~/Areas/Blog/Views/BlogPhotoes/Delete.cshtml" class="badge badge-danger">Delete</a>
                                  </div>
                              </div>
                          </div>
                      </div>
                  </div>
              </div>
      }

*Jump to: [Front End Stories](#front-end-stories), [Back End Stories](#back-end-stories), [Other Skills](#other-skills-learned), [Page Top](#liveproject)*


## Front End Stories
* [Style Create and Edit Pages](#style-create-and-edit-pages)
* [SignIn Page Styling](#signin-page-styling)
* [Index Flexbox](#index-flexbox)

### Style Create and Edit Pages
The form to add a photo needed to be styled, including updated buttons, text placeholders,  colors matching the client's color palette, centering the form, and button hover effects. Wrapped entire form in <div id="BlogPhoto-Create--FormContainer"> and added CSS.
Added text placeholder within the Razor syntax for the form input as well as style IDs for the form buttons:
      <div class="col-md-10">
                        @Html.EditorFor(model => model.Title, new { htmlAttributes = new { id = "BlogPhoto-Create--TitleEnter", placeholder = "Enter Photo Title",            @class = "form-control" } })
                        @Html.ValidationMessageFor(model => model.Title, "", new { @class = "text-danger" })
                    </div>
Created CSS for the section:
      /*Style BlogPhoto Form Container*/
      #BlogPhoto-Create--FormContainer {
          display: block;
          align-content: center;
          margin-left: 100px;
          margin-right: 220px;
          border-block: solid;
          border-block-color: var(--secondary-color);
          border-right: solid;
          border-right-color: var(--secondary-color);
          border-left: solid;
          border-left-color: var(--secondary-color);
      }

      #BlogPhoto-Edit--FormContainer {
          display: block;
          align-content: center;
          margin-left: 100px;
          margin-right: 220px;
          border-block: solid;
          border-block-color: var(--secondary-color);
          border-right: solid;
          border-right-color: var(--secondary-color);
          border-left: solid;
          border-left-color: var(--secondary-color);
      }
      /* Style submit button */
      #BlogPhoto-Create--CreateButton {
          display: flex;
          background-color: var(--main-color);
          font-weight: bolder;
          margin-bottom: 10px;
          margin-left: 10px;
          align-items: baseline;
          justify-content: center;
          text-align: center;
          border-radius: 10px 10px;
          width: 750px;
      }

          /* Style button hover effect */
          #BlogPhoto-Create--CreateButton:hover {
              box-shadow: 0px 0px 5px var(--light-color);
          }

      /* Style BackToList button */
      #BlogPhoto-BackToList {
          display: flex;
          background-color: var(--main-color--light);
          color: var(--light-color);
          font-weight: bolder;
          margin-bottom: 10px;
          margin-left: 10px;
          align-items: baseline;
          justify-content: center;
          text-align: center;
          border-radius: 10px 10px;
          width: 750px;
      }

          /* Button hover effect */
          #BlogPhoto-BackToList:hover {
              box-shadow: 0px 0px 5px var(--light-color);
          }

      /* Style Edit Button*/
      #BlogPhoto-Edit {
          display: flex;
          background-color: var(--main-color);
          font-weight: bolder;
          margin-bottom: 10px;
          margin-left: 10px;
          align-items: baseline;
          justify-content: center;
          text-align: center;
          border-radius: 10px 10px;
          width: 750px;
      }

          /* Style button hover effect */
          #BlogPhoto-Edit:hover {
              box-shadow: 0px 0px 5px var(--light-color);
          }

      #BlogPhoto-Create--TitleEnter {
          box-shadow: none;
      }

          #BlogPhoto-Create--TitleEnter:focus {
              box-shadow: none;
              border: 2px solid var(--main-color--light);
          }

      #BlogPhoto-Edit--TitleEnter {
          box-shadow: none;
      }

          #BlogPhoto-Edit--TitleEnter:focus {
              box-shadow: none;
              border: 2px solid var(--main-color--light);
          }
          
### SignIn Page Styling
This task was to style the page that will show all the developers from the Tech Academy who worked on this project. The default display was a list, the request was to have the names shown in pill-shaped backgrounds with no wrap text or overflow. The list of names were all separate <p> tags within a div titled "div class="home-signin--container" id="PersonList">
Went to the main site CSS sheet to style the list and p tags, creating 4 even columns of names without overflow:

      #PersonList {
          column-count: 4;
      }

      #PersonList p {
          width: 160px;
          background-color: var(--main-color);
          text-align: center;
          height: fit-content;
          border-radius: 14px 14px;
          flex: 1;
          white-space: nowrap;
          overflow: hidden;
          text-overflow: ellipsis;
      }
  
### Index Flexbox
The photo index page needed styled so each photo would display in a grid, and once hovered, "edit" and "delete" buttons would show. The flexbox HTML was shown above in step [Photo Storage & Retrieval](#photo-storage-and-retrieval). The CSS to style the photos added is here (this was a work in progress with the sprint ended and was not a required task):
  
      .BlogPhoto-Index--card-body{
           display: none;
      }

      .BlogPhoto-Index--card{
          display: block;
          opacity: 1;
          transition: .5s ease;
          backface-visibility: hidden;
      }

      .BlogPhoto-Index--card:hover .BlogPhoto-Index--card-body {
          display: block;
      }

      .BlogPhoto-Index--Flexbox {

          display: flex;
          flex-flow: row wrap;
          flex-direction: row;
          flex-basis:30%;
          justify-content: space-around;

      }
      .BlogPhoto-Index--Overlay {
          position: relative;
          opacity: 0;

      }
      .BlogPhoto-Index--card:hover .BlogPhoto-Index--Overlay{
          opacity: 20%;
      }


*Jump to: [Front End Stories](#front-end-stories), [Back End Stories](#back-end-stories), [Other Skills](#other-skills-learned), [Page Top](#liveproject)*

## Other Skills Learned
* Utilized version control features more effectively to avoid merge conflicts and outdated local repositories by pulling from the master and merging into my current local repository.
* Collaborated with members of a team to find solutions to problems not yet seen in my prior experience.
* Used Microsoft Azure DevOps to manage user stories / cards and learned the aspects of the workflow. This also helped me see where every step of the project was and what had been completed, providing valuable insight as to how other developers were approaching their tasks.  
* Worked with a project manager to ensure tasks being completed and files being edited were not in conflict with work other developers were completing.
* Gained real-life project experience and was able to see how Agile practices work in a practical work setting.
  * Participated in aspects of Agile / SCRUM Practices:
    * 1 Sprint Planning Meeting
    * 9 Daily Stand-ups
    * 2 Code Retrospectives
    * 1 Code Summary
  
*Jump to: [Front End Stories](#front-end-stories), [Back End Stories](#back-end-stories), [Other Skills](#other-skills-learned), [Page Top](#liveproject)*
