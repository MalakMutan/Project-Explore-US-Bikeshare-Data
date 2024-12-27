

import pandas as pd
import time

def display_raw_data(df):
    """
    Displays 5 rows of raw data at a time upon user request.
    """
    i = 0
    pd.set_option('display.max_columns', 200)
    while True:
        raw = input("\nWould you like to view 5 rows of raw data? Enter 'yes' or 'no'.\n").lower()
        if raw == 'no':
            break
        elif raw == 'yes':
            print(df.iloc[i:i+5])
            i += 5
            if i >= len(df):
                print("\nNo more data to display.")
                break
        else:
            print("\nInvalid input. Please enter 'yes' or 'no'.")

def time_stats(df):
    """Displays statistics on the most frequent times of travel."""
    print('\nCalculating The Most Frequent Times of Travel...\n')
    start_time = time.time()

    # Most common month
    most_common_month = df['month'].mode()[0]
    print('Most Common Month:', most_common_month)

    # Most common day of the week
    most_common_day = df['day_of_week'].mode()[0]
    print('Most Common Day of Week:', most_common_day)

    # Most common start hour
    most_common_start_hour = df['Start Time'].dt.hour.mode()[0]
    print('Most Common Start Hour:', most_common_start_hour)

    print("\nThis took %s seconds." % (time.time() - start_time))
    print('-'*40)

def station_stats(df):
    """Displays statistics on the most popular stations and trip."""
    print('\nCalculating The Most Popular Stations and Trip...\n')
    start_time = time.time()

    # Most commonly used start station
    start_station = df['Start Station'].value_counts().idxmax()
    print('Most Commonly used start station:', start_station)

    # Most commonly used end station
    end_station = df['End Station'].value_counts().idxmax()
    print('Most Commonly used end station:', end_station)

    # Most common trip (start station + end station)
    most_common_trip = df.groupby(['Start Station', 'End Station']).size().idxmax()
    print('Most Common Trip:', most_common_trip)

    print("\nThis took %s seconds." % (time.time() - start_time))
    print('-'*40)

def trip_duration_stats(df):
    """Displays statistics on the total and average trip duration."""
    print('\nCalculating Trip Duration...\n')
    start_time = time.time()

    # Total travel time
    total_travel_time = df['Trip Duration'].sum()
    print('Total travel time:', round(total_travel_time / 86400, 2), "days")

    # Mean travel time
    mean_travel_time = df['Trip Duration'].mean()
    print('Mean travel time:', round(mean_travel_time / 60, 2), "minutes")

    print("\nThis took %s seconds." % (time.time() - start_time))
    print('-'*40)

def user_stats(df):
    """Displays statistics on bikeshare users."""
    print('\nCalculating User Stats...\n')
    start_time = time.time()

    # User types
    user_types = df['User Type'].value_counts()
    print('User Types:\n', user_types)

    # Gender types
    try:
        gender_types = df['Gender'].value_counts()
        print('\nGender Types:\n', gender_types)
    except KeyError:
        print("\nGender Types:\nNo data available.")

    # Birth year statistics
    try:
        earliest_year = int(df['Birth Year'].min())
        print('\nEarliest Year:', earliest_year)
        most_recent_year = int(df['Birth Year'].max())
        print('Most Recent Year:', most_recent_year)
        most_common_year = int(df['Birth Year'].mode()[0])
        print('Most Common Year:', most_common_year)
    except KeyError:
        print("\nBirth Year:\nNo data available.")

    print("\nThis took %s seconds." % (time.time() - start_time))
    print('-'*40)

def load_data(city, month, day):
    """
    Loads data for the specified city and filters by month and day if applicable.
    """
    # Load data into a DataFrame
    df = pd.read_csv(city)

    # Convert the Start Time column to datetime
    df['Start Time'] = pd.to_datetime(df['Start Time'])

    # Extract month and day of week from Start Time to create new columns
    df['month'] = df['Start Time'].dt.month
    df['day_of_week'] = df['Start Time'].dt.day_name()

    # Filter by month if applicable
    if month != 'all':
        months = ['january', 'february', 'march', 'april', 'may', 'june']
        month = months.index(month) + 1
        df = df[df['month'] == month]

    # Filter by day of week if applicable
    if day != 'all':
        df = df[df['day_of_week'] == day.title()]

    return df

def get_filters():
    """
    Asks user to specify a city, month, and day to analyze.
    Returns:
        (str) city - name of the city to analyze
        (str) month - name of the month to filter by, or "all" to apply no month filter
        (str) day - name of the day of week to filter by, or "all" to apply no day filter
    """
    print('Hello! Let\'s explore some US bikeshare data!')

    # Get user input for city (chicago, new york city, washington)
    city = input("Enter the name of the city to analyze (chicago, new york city, washington): ").lower()

    # Get user input for month (all, january, february, ... , june)
    month = input("Enter the name of the month to filter by, or 'all' for no filter: ").lower()

    # Get user input for day of week (all, monday, tuesday, ... sunday)
    day = input("Enter the name of the day of week to filter by, or 'all' for no filter: ").lower()

    return city, month, day

def main():
    while True:
        city, month, day = get_filters()
        df = load_data(city, month, day)

        time_stats(df)
        station_stats(df)
        trip_duration_stats(df)
        user_stats(df)
        display_raw_data(df)

        restart = input('\nWould you like to restart? Enter yes or no.\n').lower()
        if restart != 'yes':
            break

if __name__ == "__main__":
    main()
