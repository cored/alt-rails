
### Example: Bounded Contexts in a Movie Booking Management System

In this example, we will have two bounded contexts: **Movies** and **BookMovie** within a single gem called *booking_management*.

#### Gem Specification

```ruby
# booking_management.gemspec
Gem::Specification.new do |spec|
	spec.name        = "booking_management"
	spec.version     = "0.1.0"
	spec.authors     = ["Your Name"]
	spec.summary     = "Booking Management System"

	spec.files = Dir["{app,lib}/**/*", "MIT-LICENSE", "Rakefile", "README.md"]

	spec.add_dependency "rails", "~> 6.1.0"
	spec.add_dependency "dry-struct"
	spec.add_dependency "dry-types"
end
```

### Movie Bounded Context

```ruby
# booking_management/lib/booking_management/movie.rb
module BookingManagement
	class Movie < Dry::Struct
		attribute :id, Types::Integer
		attribute :title, Types::String
		attribute :description, Types::String
		attribute :duration, Types::Integer
	end
end

# booking_management/lib/booking_management/movies.rb
module BookingManagement
	class Movies
		def initialize(data_source=::Movie)
			@data_soource = Movie
		end

		def add(movie)
			data_source.create(movie.to_h)
		end

		def find_by_id(id)
			@movies.find { |movie| movie.id == id }
		end
	end
end
```

### BookMovie Class

```ruby
# booking_management/lib/booking_management/book_movie.rb
module BookingManagement
	class BookMovie
		def self.call(movie_params, user_id)
			movies = Movies.new

			# Create a new movie instance
			movie = Movie.new(
				id: SecureRandom.uuid,  # Simulating a movie ID
				title: movie_params[:title],
				description: movie_params[:description],
				duration: movie_params[:duration]
			)

			# Simulating adding the movie to the data store
			movies.add(movie)

			# Create a booking instance (if needed)
			booking = Booking.new(
				id: SecureRandom.uuid,  # Simulating a booking ID
				movie_id: movie.id,
				user_id: user_id,
				date: movie_params[:date],
				time: movie_params[:time]
			)

			# Simulating adding the booking to the data store (replace with actual implementation)
			# @bookings.add(booking)

			{ movie: movie, booking: booking }
		end
	end
end
```

### Usage Example

```ruby
# app/controllers/bookings_controller.rb
class BookingsController < ApplicationController
	def create
		begin
			result = BookingManagement::BookMovie.call(
				{
					title: params[:title],
					description: params[:description],
					duration: params[:duration],
					date: params[:date],
					time: params[:time]
				},
				current_user.id
			)

			movie = result[:movie]
			booking = result[:booking]

			redirect_to booking_path(booking)
		rescue => e
			flash[:error] = e.message
			redirect_to movies_path
		end
	end
end
```
