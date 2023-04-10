# API Versions:
We implemented different API versions utilizing different version numbers being passed via the request’s headers. We used this in 3 spots - getting a teacher’s students, getting a student’s practice score, and getting recordings. Here is an example getting a teacher’s students
## __Steps__
1. Create two HttpClients with their set headers and register your service, injecting both client in
    - image
    - `builder.Services.AddSingleton<PianoLessonsService>(provider =>
		{
			var clientV1 = provider.GetRequiredService<IHttpClientFactory>().CreateClient("v1");
			var clientV2 = provider.GetRequiredService<IHttpClientFactory>().CreateClient("v2");

			return new PianoLessonsService(clientV1, clientV2);
		});`
2. __Inject both client into your service via the contructor__
    - `private readonly HttpClient clientV1;
	private readonly HttpClient clientV2;

    public PianoLessonsService(HttpClient clientV1, HttpClient clientV2)
    {
        this.clientV1 = clientV1;
		this.clientV2 = clientV2;
	}`
3. __Make your request with the client that has your desired version__
    - `return await clientV1.GetFromJsonAsync<List<Student>>($"api/PianoLessons/students/{teacherId}");
	    return await clientV2.GetFromJsonAsync<List<Student>>($"api/PianoLessons/students/{teacherId}");`
4. __We created this class so we can declare the version as a header attribute__
    - image 2
5. __Create an endpoint for each desired version like so__
    - image 3
    - The request should automatically map to the endpoint that has the desired version
    - The functions return a IActionResult now, so merely wrap what you want to return in an Ok() function
