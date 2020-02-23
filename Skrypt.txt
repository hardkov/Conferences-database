
CREATE TABLE [dbo].[Conferences](
 [ConferenceID] integer IDENTITY(1,1) NOT NULL,
 [ConferenceName] varchar(40) NOT NULL ,
 [ClientID] integer NOT NULL,
 [StartTime] date NOT NULL,
 [EndTime] date NOT NULL,
 [Price] integer NOT NULL,
 [StudentDiscount] float(10) NOT NULL,
 CONSTRAINT [PK_Conferences] PRIMARY KEY CLUSTERED ([ConferenceID] ASC)
     WITH (PAD_INDEX = OFF, STATISTICS_NORECOMPUTE = OFF, IGNORE_DUP_KEY = OFF, ALLOW_ROW_LOCKS = ON,
         ALLOW_PAGE_LOCKS = ON) ON [PRIMARY]
) ON [PRIMARY]

ALTER TABLE Conferences ADD CONSTRAINT [UniqueConferenceName_Conferences] UNIQUE NONCLUSTERED ([ConferenceName] ASC)
    WITH (PAD_INDEX = OFF, STATISTICS_NORECOMPUTE = OFF, IGNORE_DUP_KEY = OFF, ALLOW_ROW_LOCKS = ON,
        ALLOW_PAGE_LOCKS = ON) ON [PRIMARY];

ALTER TABLE [dbo].[Conferences] WITH CHECK ADD CONSTRAINT [fractionalDiscountValue] check (([StudentDiscount] >= 0 and [StudentDiscount] <= 1))


CREATE TABLE [dbo].[Clients] (
 [ClientID] integer IDENTITY(1,1) NOT NULL,
 [Phone] varchar(40)  NOT NULL,
 [Email] varchar(40) NOT NULL,
 [Address] varchar(40) NOT NULL,
 CONSTRAINT [PK_Clients] PRIMARY KEY CLUSTERED ([ClientID] ASC)
     WITH (PAD_INDEX = OFF, STATISTICS_NORECOMPUTE = OFF, IGNORE_DUP_KEY = OFF, ALLOW_ROW_LOCKS = ON,
         ALLOW_PAGE_LOCKS = ON) ON [PRIMARY],
 CONSTRAINT [UniqueEMail_Clients] UNIQUE NONCLUSTERED ([Email] ASC)
     WITH (PAD_INDEX = OFF, STATISTICS_NORECOMPUTE = OFF, IGNORE_DUP_KEY = OFF, ALLOW_ROW_LOCKS = ON,
         ALLOW_PAGE_LOCKS = ON) ON [PRIMARY]
) ON [PRIMARY]


CREATE TABLE [dbo].[Companies] (
 [ClientID] [integer] NOT NULL,
 [CompanyName] varchar(40) NOT NULL ,
 [NIP] varchar(15) NOT NULL,
 CONSTRAINT [PK_Companies] PRIMARY KEY CLUSTERED ([ClientID] ASC)
     WITH (PAD_INDEX = OFF, STATISTICS_NORECOMPUTE = OFF, IGNORE_DUP_KEY = OFF, ALLOW_ROW_LOCKS = ON,
         ALLOW_PAGE_LOCKS = ON) ON [PRIMARY],

 CONSTRAINT [UniqueNIP_Clients] UNIQUE NONCLUSTERED ([NIP] ASC)
     WITH (PAD_INDEX = OFF, STATISTICS_NORECOMPUTE = OFF, IGNORE_DUP_KEY = OFF, ALLOW_ROW_LOCKS = ON,
             ALLOW_PAGE_LOCKS = ON) ON [PRIMARY]
) ON [PRIMARY]

ALTER TABLE [dbo].[Companies] WITH CHECK ADD CONSTRAINT [FK_Companies_ClientID] FOREIGN KEY([ClientID])
 REFERENCES [dbo].[Clients] ([ClientID]);


CREATE TABLE [dbo].[Individuals] (
    [ClientID] [integer] NOT NULL,
    [FirstName] varchar(40) NOT NULL ,
    [LastName] varchar(40) NOT NULL,
    CONSTRAINT [PK_Individuals] PRIMARY KEY CLUSTERED ([ClientID] ASC)
        WITH (PAD_INDEX = OFF, STATISTICS_NORECOMPUTE = OFF, IGNORE_DUP_KEY = OFF, ALLOW_ROW_LOCKS = ON,
            ALLOW_PAGE_LOCKS = ON) ON [PRIMARY],
) ON [PRIMARY]

ALTER TABLE [dbo].[Individuals] WITH CHECK ADD CONSTRAINT [FK_Individuals_ClientID] FOREIGN KEY([ClientID])
 REFERENCES [dbo].[Clients] ([ClientID]);


CREATE TABLE [dbo].[ConferenceDays](
    [ConferenceDayID] [int] IDENTITY(1,1) NOT NULL,
    [ConferenceID] [int] NOT NULL,
    [ParticipantsLimit] [int] NOT NULL,
    [Date] [date] NOT NULL,
    CONSTRAINT [PK_ConferenceDays] PRIMARY KEY CLUSTERED ([ConferenceDayID] ASC)
        WITH (PAD_INDEX = OFF, STATISTICS_NORECOMPUTE = OFF, IGNORE_DUP_KEY = OFF, ALLOW_ROW_LOCKS = ON,
            ALLOW_PAGE_LOCKS = ON) ON [PRIMARY]
) ON [PRIMARY]

ALTER TABLE [dbo].[ConferenceDays] WITH CHECK ADD CONSTRAINT [FK_ConferenceDays_Conferences] FOREIGN KEY([ConferenceID])
 REFERENCES [dbo].[Conferences] ([ConferenceID])

ALTER TABLE [dbo].[ConferenceDays] CHECK CONSTRAINT [FK_ConferenceDays_Conferences]

ALTER TABLE [dbo].[ConferenceDays] WITH CHECK ADD CONSTRAINT [CK_ParticipantsLimit] CHECK
 (([ParticipantsLimit]>(0)))

ALTER TABLE [dbo].[ConferenceDays] CHECK CONSTRAINT [CK_ParticipantsLimit]

CREATE TABLE [dbo].[Workshops](
   [WorkshopID] [int] IDENTITY(1,1) NOT NULL,
   [ConferenceID] [int] NOT NULL,
   [ConferenceDayID] [int] NOT NULL,
   [WorkshopName] [varchar](100) NOT NULL,
   [ParticipantsLimit] [int] NOT NULL,
   [StartTime] [time] NOT NULL ,
   [EndTime] [time] NOT NULL ,
   [Price] [money] NOT NULL,
   CONSTRAINT [PK_Workshops] PRIMARY KEY CLUSTERED ([workshopID] ASC)
       WITH (PAD_INDEX = OFF, STATISTICS_NORECOMPUTE = OFF, IGNORE_DUP_KEY = OFF, ALLOW_ROW_LOCKS = ON, ALLOW_PAGE_LOCKS = ON) ON [PRIMARY]
) ON [PRIMARY]

ALTER TABLE [dbo].[Workshops] WITH CHECK ADD CONSTRAINT [FK_Workshops_ConferenceDays] FOREIGN KEY([ConferenceDayID])
 REFERENCES [dbo].[ConferenceDays] ([ConferenceDayID]);

ALTER TABLE [dbo].[Workshops] CHECK CONSTRAINT [FK_Workshops_ConferenceDays]

ALTER TABLE [dbo].[Workshops] WITH CHECK ADD CONSTRAINT [FK_Workshops_Conferences] FOREIGN KEY([ConferenceID])
 REFERENCES [dbo].[Conferences] ([ConferenceID]);

ALTER TABLE [dbo].[Workshops] CHECK CONSTRAINT [FK_Workshops_Conferences]

ALTER TABLE [dbo].[Workshops] WITH CHECK ADD CONSTRAINT [positiveLimitNumber] CHECK
 (([ParticipantsLimit]>(0)))

ALTER TABLE [dbo].[Workshops] CHECK CONSTRAINT [positiveLimitNumber]

ALTER TABLE [dbo].[Workshops] WITH CHECK ADD CONSTRAINT [notNegativePrice] CHECK (([Price]>=(0)))

ALTER TABLE [dbo].[Workshops] CHECK CONSTRAINT [notNegativePrice]

CREATE TABLE [dbo].[Participants](
  [ParticipantID] [int] IDENTITY(1,1) NOT NULL,
  [FirstName] [varchar](40),
  [LastName] [varchar](40),
  [Address] [varchar](40),
  [Phone] [varchar](40),
  CONSTRAINT [PK_Participants] PRIMARY KEY CLUSTERED ([ParticipantID] ASC)
      WITH (PAD_INDEX = OFF, STATISTICS_NORECOMPUTE = OFF, IGNORE_DUP_KEY = OFF, ALLOW_ROW_LOCKS = ON,
          ALLOW_PAGE_LOCKS = ON) ON [PRIMARY]
) ON [PRIMARY]

CREATE TABLE [dbo].[ConferenceParticipants](
 [ParticipantID] [int] NOT NULL,
 [ConferenceDayID] [int] NOT NULL,
 [ConferenceID] [int] NOT NULL,
 [Cancelled] [int] NOT NULL,
 CONSTRAINT [PK_ConferenceParticipants] PRIMARY KEY CLUSTERED ([ParticipantID] ASC, [ConferenceDayID] ASC)
     WITH (PAD_INDEX = OFF, STATISTICS_NORECOMPUTE = OFF, IGNORE_DUP_KEY = OFF, ALLOW_ROW_LOCKS = ON,
         ALLOW_PAGE_LOCKS = ON) ON [PRIMARY],
) ON [PRIMARY]

ALTER TABLE [dbo].[ConferenceParticipants] WITH CHECK ADD CONSTRAINT
 [FK_ConferenceParticipants_Participants] FOREIGN KEY([ParticipantID])
     REFERENCES [dbo].[Participants] ([ParticipantID])

ALTER TABLE [dbo].[ConferenceParticipants] CHECK CONSTRAINT [FK_ConferenceParticipants_Participants]

ALTER TABLE [dbo].[ConferenceParticipants] WITH CHECK ADD CONSTRAINT
 [FK_ConferenceParticipants_ConferenceDays] FOREIGN KEY([ConferenceDayID])
     REFERENCES [dbo].[ConferenceDays] ([ConferenceDayID])

ALTER TABLE [dbo].[ConferenceParticipants] CHECK CONSTRAINT [FK_ConferenceParticipants_ConferenceDays]

CREATE TABLE [dbo].[WorkshopParticipants](
 [WorkshopID] [int] NOT NULL,
 [ParticipantID] [int] NOT NULL,
 [ConferenceDayID] [int] NOT NULL,
 [Cancelled] [int] NOT NULL,
 CONSTRAINT [PK_WorkshopParticipants] PRIMARY KEY CLUSTERED ([ParticipantID] ASC, [WorkshopID] ASC)
         WITH (PAD_INDEX = OFF, STATISTICS_NORECOMPUTE = OFF, IGNORE_DUP_KEY = OFF, ALLOW_ROW_LOCKS = ON,
             ALLOW_PAGE_LOCKS = ON) ON [PRIMARY],
) ON [PRIMARY]

ALTER TABLE [dbo].[WorkshopParticipants] WITH CHECK ADD CONSTRAINT
 [FK_WorkshopParticipants_Participants] FOREIGN KEY([ParticipantID])
     REFERENCES [dbo].[Participants] ([ParticipantID])

ALTER TABLE [dbo].[WorkshopParticipants] CHECK CONSTRAINT [FK_WorkshopParticipants_Participants]

ALTER TABLE [dbo].[WorkshopParticipants] WITH CHECK ADD CONSTRAINT
 [FK_WorkshopParticipants_Workshops] FOREIGN KEY([WorkshopID])
     REFERENCES [dbo].[Workshops] ([WorkshopID])

ALTER TABLE [dbo].[WorkshopParticipants] CHECK CONSTRAINT [FK_WorkshopParticipants_Workshops]

ALTER TABLE [dbo].[WorkshopParticipants] WITH CHECK ADD CONSTRAINT
 [FK_WorkshopParticipants_ConferenceParticipants] FOREIGN KEY([ParticipantID], [ConferenceDayID])
     REFERENCES [dbo].[ConferenceParticipants] ([ParticipantID], ConferenceDayID)

ALTER TABLE [dbo].[WorkshopParticipants] CHECK CONSTRAINT [FK_WorkshopParticipants_ConferenceParticipants]

CREATE TABLE [dbo].[Students] (
 [ParticipantID] integer NOT NULL,
 [StudentCardID] varchar(40) NOT NULL,
 CONSTRAINT [PK_Students] PRIMARY KEY CLUSTERED ([ParticipantID] ASC)
     WITH (PAD_INDEX = OFF, STATISTICS_NORECOMPUTE = OFF, IGNORE_DUP_KEY = OFF, ALLOW_ROW_LOCKS = ON,
         ALLOW_PAGE_LOCKS = ON) ON [PRIMARY],
 CONSTRAINT [UniqueCardID_Students] UNIQUE NONCLUSTERED ([StudentCardID] ASC)
     WITH (PAD_INDEX = OFF, STATISTICS_NORECOMPUTE = OFF, IGNORE_DUP_KEY = OFF, ALLOW_ROW_LOCKS = ON,
         ALLOW_PAGE_LOCKS = ON) ON [PRIMARY]
) ON [PRIMARY]

ALTER TABLE [dbo].[Students] WITH CHECK ADD CONSTRAINT
 [FK_Students_Participants] FOREIGN KEY([ParticipantID])
     REFERENCES [dbo].[Participants] ([ParticipantID])

ALTER TABLE [dbo].[Students] CHECK CONSTRAINT [FK_Students_Participants]

CREATE TABLE [dbo].[ConferenceDayReservation](
    [ParticipantID] [int] NOT NULL,
    [ConferenceDayID] [int] NOT NULL,
    [ReservationDate] [date] NOT NULL,
    [DueDate] [date] NOT NULL,
    [AmountToPay] [money] NOT NULL,
    [AmountPaid] [money] NOT NULL,
    [Cancelled] [int] NOT NULL,
    CONSTRAINT [PK_ConfDayBooking] PRIMARY KEY CLUSTERED ([ParticipantID] ASC, [ConferenceDayID] ASC)
        WITH (PAD_INDEX = OFF, STATISTICS_NORECOMPUTE = OFF, IGNORE_DUP_KEY = OFF, ALLOW_ROW_LOCKS = ON,
            ALLOW_PAGE_LOCKS = ON) ON [PRIMARY]
) ON [PRIMARY]

ALTER TABLE [dbo].[ConferenceDayReservation] WITH CHECK ADD CONSTRAINT
 [FK_ConferenceDayReservation_ConferenceDays] FOREIGN KEY([ConferenceDayID])
     REFERENCES [dbo].[ConferenceDays] ([ConferenceDayID])

ALTER TABLE [dbo].[ConferenceDayReservation] CHECK CONSTRAINT [FK_ConferenceDayReservation_ConferenceDays]

ALTER TABLE [dbo].[ConferenceDayReservation] WITH CHECK ADD CONSTRAINT
 [FK_ConferenceDayReservation_Participants] FOREIGN KEY([ParticipantID])
     REFERENCES [dbo].[Participants] ([ParticipantID])

ALTER TABLE [dbo].[ConferenceDayReservation] CHECK CONSTRAINT [FK_ConferenceDayReservation_Participants]


CREATE TABLE [dbo].[WorkshopReservation](
  [ParticipantID] [int] NOT NULL,
  [WorkshopID] [int] NOT NULL,
  [ConferenceDayID] [int] NOT NULL ,
  [ReservationDate] [date] NOT NULL,
  [DueDate] [date] NOT NULL,
  [AmountToPay] [money] NOT NULL,
  [AmountPaid] [money] NOT NULL,
  [Cancelled] [int] NOT NULL,
  CONSTRAINT [PK_WorkshopReservation] PRIMARY KEY CLUSTERED ([ParticipantID] ASC, [WorkshopID] ASC)
      WITH (PAD_INDEX = OFF, STATISTICS_NORECOMPUTE = OFF, IGNORE_DUP_KEY = OFF, ALLOW_ROW_LOCKS = ON,
          ALLOW_PAGE_LOCKS = ON) ON [PRIMARY]
) ON [PRIMARY]

ALTER TABLE [dbo].[WorkshopReservation] WITH CHECK ADD CONSTRAINT
 [FK_WorkshopReservation_ConferenceDayReservation] FOREIGN KEY([ParticipantID], [ConferenceDayID])
     REFERENCES [dbo].[ConferenceDayReservation] ([ParticipantID], [ConferenceDayID])

ALTER TABLE [dbo].[WorkshopReservation] CHECK CONSTRAINT [FK_WorkshopReservation_ConferenceDayReservation]

ALTER TABLE [dbo].[ConferenceDayReservation] WITH CHECK ADD CONSTRAINT
 [FK_WorkshopReservation_Participants] FOREIGN KEY([ParticipantID])
     REFERENCES [dbo].[Participants] ([ParticipantID])

ALTER TABLE [dbo].[ConferenceDayReservation] CHECK CONSTRAINT [FK_WorkshopReservation_Participants]

ALTER TABLE [dbo].[WorkshopReservation] WITH CHECK ADD CONSTRAINT
 [FK_WorkshopReservation_Workshops] FOREIGN KEY([WorkshopID])
     REFERENCES [dbo].[Workshops] ([WorkshopID])

ALTER TABLE [dbo].[WorkshopReservation] CHECK CONSTRAINT [FK_WorkshopReservation_Workshops]

CREATE TABLE [dbo].[ConfDiscounts](
   [DiscountID] [int] IDENTITY(1,1) NOT NULL,
   [ConferenceID] [int] NOT NULL,
   [ToDate] [date] NOT NULL,
   [Discount] [float] NOT NULL,
   CONSTRAINT [PK_ConfDiscounts] PRIMARY KEY CLUSTERED ([DiscountID] ASC)
       WITH (PAD_INDEX = OFF, STATISTICS_NORECOMPUTE = OFF, IGNORE_DUP_KEY = OFF, ALLOW_ROW_LOCKS = ON,
           ALLOW_PAGE_LOCKS = ON) ON [PRIMARY]
) ON [PRIMARY]

ALTER TABLE [dbo].[ConfDiscounts] WITH CHECK ADD CONSTRAINT [FK_ConfDiscounts_Conferences] FOREIGN KEY([ConferenceID]) REFERENCES [dbo].[Conferences] ([ConferenceID])

ALTER TABLE [dbo].[ConfDiscounts] CHECK CONSTRAINT [FK_ConfDiscounts_Conferences]

ALTER TABLE [dbo].[ConfDiscounts] WITH CHECK ADD CONSTRAINT [CK_Discount] CHECK
(([Discount]>=(0) and [Discount]<=(1)))

ALTER TABLE [dbo].[ConfDiscounts] CHECK CONSTRAINT [CK_Discount]

CREATE TABLE [dbo].[CompanyEmployees] (
[ParticipantID] integer NOT NULL,
[CompanyName] varchar(40) NOT NULL,
CONSTRAINT [PK_CompanyEmployees] PRIMARY KEY CLUSTERED ([ParticipantID] ASC)
    WITH (PAD_INDEX = OFF, STATISTICS_NORECOMPUTE = OFF, IGNORE_DUP_KEY = OFF, ALLOW_ROW_LOCKS = ON,
        ALLOW_PAGE_LOCKS = ON) ON [PRIMARY],
) ON [PRIMARY]

ALTER TABLE [dbo].[CompanyEmployees] WITH CHECK ADD CONSTRAINT
[FK_CompanyEmployees_Participants] FOREIGN KEY([ParticipantID])
    REFERENCES [dbo].[Participants] ([ParticipantID])

ALTER TABLE [dbo].[CompanyEmployees] CHECK CONSTRAINT [FK_CompanyEmployees_Participants]

ALTER TABLE [dbo].[Conferences] WITH CHECK ADD CONSTRAINT [FK_Conferences_ClientID] FOREIGN KEY([ClientID])
 REFERENCES [dbo].[Clients] ([ClientID]);






create function getConfDiscount (@ConferenceID int, @Date date)
returns float
as
begin
  return (
      select top 1 Discount
      from ConfDiscounts
      where  ConferenceID = @ConferenceID and @Date <= toDate
      order by ToDate asc
  )

end
go

CREATE function getConferenceDayFreeSeats (@ConferenceDayID int)
returns int
as
begin
  declare @Limit as int
  declare @Participants as int
  declare @Reservations as int

  set @Limit = (
     select ParticipantsLimit
     from ConferenceDays
     where ConferenceDayID = @ConferenceDayID
  )
  set @Participants = (
     select count(ParticipantID)
     from ConferenceParticipants
     where ConferenceDayID = @ConferenceDayID and Cancelled = -1
  )
  set @Reservations = (
      select count(ParticipantID)
      from ConferenceDayReservation
      where ConferenceDayID = ConferenceDayID and Cancelled = -1
  )

  return @Limit - @Participants - @Reservations
end
go

CREATE FUNCTION getConferenceDayID (
  @dateOfDay date,
  @conferenceIdParameter int
) RETURNS int
BEGIN
  RETURN (select ConferenceDayID
      from ConferenceDays
      inner join Conferences C on ConferenceDays.ConferenceID = C.ConferenceID
      where C.ConferenceID = @conferenceIdParameter and ConferenceDays.Date = @dateOfDay)
END
go



create function getConferenceID (@ConferenceName varchar(40))
returns int
as
begin
  return (
      select ConferenceID
      from Conferences
      where ConferenceName = @ConferenceName
  )
end
go


CREATE function getConferencePrice (@ConferenceID int)
returns money
as
begin
  return (
         select Price
         from Conferences
         where ConferenceID = @ConferenceID
  )
end
go

create function getStudentDiscount (@ConferenceID int)
returns float
as
begin
  return (
      select StudentDiscount
      from Conferences
      where ConferenceID = @ConferenceID
  )
end
go


CREATE function getWorkshopFreeSeats (@WorkshopID int)
returns int
begin
  declare @Participants as int
  declare @Limit as int
  declare @Reservation as int

  set @Limit = (
      select ParticipantsLimit
      from Workshops
      where WorkshopID = @WorkshopId
  )
  set @Participants = (
      select count(ParticipantID)
      from WorkshopParticipants
      where WorkshopID = @WorkshopID and Cancelled = -1
  )
  set @Reservation = (
      select count(ParticipantID)
      from WorkshopReservation
      where WorkshopID = @WorkshopID and Cancelled = -1
  )

  return @Limit - @Participants - @Reservation
end
go

create function getWorkshopID (@Date date, @ConferenceName varchar(40), @WorkshopName varchar(40))
returns int
as
begin
  declare @WorkshopID as int
  declare @ConferenceDayID as int
  declare @ConferenceID as int

  set @ConferenceID = dbo.getConferenceID (@ConferenceName)
  set @ConferenceDayID = dbo.getConferenceDayID (@Date, @ConferenceID)
  set @WorkshopID = (
      select WorkshopID
      from Workshops
      where ConferenceDayID = @ConferenceDayID and WorkshopName = @WorkshopName
  )
  return @WorkshopID
end
go

CREATE function getWorkshopPrice (@WorkshopID int)
returns money
as
begin
  return (
      select Price
      from Workshops
      where WorkshopID = @WorkshopID
  )
end
go


CREATE function isAlreadyParticipating(@ParticipantID int, @WorkshopID int)
returns int
as
begin
  declare @result as int
  declare @WorkshopsInTheSameTimeCount as int
  declare @StartTime as time
  declare @EndTime as time

  set @StartTime = (
      select StartTime
      from Workshops
      where WorkshopID = @WorkshopID
  )
  set @EndTime = (
      select EndTime
      from Workshops
      where WorkshopID = @WorkshopID
  )
  set @WorkshopsInTheSameTimeCount = (
      select count(WorkshopID)
      from Workshops
      where WorkshopID in (
          select WorkshopID
          from WorkshopParticipants
          where ParticipantID = @ParticipantID and Cancelled = -1
      )
      and ((StartTime <= @StartTime and EndTime > @StartTime) or (StartTime > @StartTime and @EndTime > StartTime))
  )
  if @WorkshopsInTheSameTimeCount > 0
  begin
      set @result = 10
  end
  else
  begin
      set @result = -10
  end

  return @result

end
go

create function isConferenceParticipant (@ConferenceDayID int, @ParticipantID int)
returns int
as
begin
  declare @isConferenceParticipant as int
  declare @result as int

  set @result = (
      select ParticipantID
      from ConferenceParticipants
      where ParticipantID = @ParticipantID and ConferenceDayID = @ConferenceDayID
  )
  if @result is null
  begin
      set @isConferenceParticipant = -10
  end
  else
  begin
      set @isConferenceParticipant = 10
  end

  return @isConferenceParticipant
end
go

create function isStudent (@ParticipantID int)
returns int
as
begin
  declare @isStudent as int
  declare @student as int
  set @student = (
      select ParticipantID
      from Students
      where ParticipantID = @ParticipantID
  )
  if @student is null
  begin
      set @isStudent = -10
  end
  else
  begin
      set @isStudent = 10
  end

  return @isStudent
end
go

















CREATE procedure Add_Client_Company
  @Phone varchar(40),
  @Email varchar(40),
  @Address varchar(40),
  @CompanyName varchar(40),
  @NIP varchar(15)
  as
  begin
      set nocount on;
      insert into Clients(Phone, Email, Address)
      values (@Phone, @Email, @Address)
      declare @ClientID as int
      set @ClientID = (
          select ClientID
          from Clients
          where Email = @Email
      )

      insert into Companies(ClientID, CompanyName, NIP)
      values (@ClientID, @CompanyName, @NIP)

  end
go


CREATE procedure Add_Client_Individual
  @Phone varchar(40),
  @Email varchar(40),
  @Address varchar(40),
  @FirstName varchar(40),
  @LastName varchar(40)
  as
  begin
      set nocount on;
      insert into Clients(Phone, Email, Address)
      values (@Phone, @Email, @Address)
      declare @ClientID as int
      set @ClientID = (
          select ClientID
          from Clients
          where Email = @Email
      )

      insert into Individuals(ClientID, FirstName, LastName)
      values (@ClientID, @FirstName, @LastName)
  end
go


create procedure Add_Company_Employee
  @CompanyName varchar(40),
  @ParticipantID int
  as
  begin
      set nocount on;
      set @ParticipantID = (
          select ParticipantID
          from Participants
          where ParticipantID = @ParticipantID
      )
      set @CompanyName = (
          select CompanyName
          from Companies
          where CompanyName = @CompanyName
      )
      insert into CompanyEmployees(ParticipantID, CompanyName)
      values (@ParticipantID, @CompanyName)
  end
go


CREATE PROCEDURE Add_Conference
  @ConferenceName varchar(40),
  @ClientID int,
  @StartTime date,
  @EndTime date,
  @Price int,
  @StudentDiscount float
  as
  begin
      set nocount on;
      IF(@StartTime > @EndTime)
      BEGIN
      ;THROW 52000, 'EndDate should not be earlier than StartDate.',1
      END
      IF(@StudentDiscount < 0 OR @StudentDiscount > 1)
      BEGIN
      ;THROW 52000, 'The discount must be between 0 and 1.',1
      END
      insert into Conferences(ConferenceName, ClientID, StartTime, EndTime, Price, StudentDiscount)
      values (@ConferenceName, @ClientID, @StartTime, @EndTime, @Price, @StudentDiscount)
  end
go

create procedure Add_Conference_Day
  @ConferenceName varchar(40),
  @Date date,
  @ParticipantLimit int
  as
  begin
      set nocount on;
      Declare @ConferenceID as int
      set @ConferenceID = (
              select ConferenceID
              from Conferences
              where ConferenceName = @ConferenceName         -- ConfName must be unique
      )
      insert into ConferenceDays(ConferenceID, Date, ParticipantsLimit)
      values (@ConferenceID, @Date, @ParticipantLimit)
  end
go

CREATE PROCEDURE Add_Conference_Day_Participant
  @ParticipantID int,
  @ConferenceDayDate date,
  @ConferenceName varchar(40)
  as
  begin
      set nocount on;
      Declare @ConferenceID as int
      Declare @ConferenceDayID as int
      set @ConferenceID = dbo.getConferenceID (@ConferenceName)
      set @ConferenceDayID = dbo.getConferenceDayID (@ConferenceDayDate, @ConferenceID)
      insert into ConferenceParticipants(ParticipantID, ConferenceDayID, ConferenceID, Cancelled)
      values (@ParticipantID, @ConferenceDayID, @ConferenceID, -1)
  end
go



CREATE procedure Add_Conference_Day_Reservation
 @ParticipantID int,
 @ConferenceName varchar(40),
 @Date date
 as
 begin
     set nocount on;
     declare @AmountToPay as float
     declare @AmountPaid as float
     declare @DueDate as date
     declare @ConfDiscount as float
     declare @StudentDiscount as float
     declare @ConferenceDayID as int
     declare @Price as float
     declare @ConferenceID as int

     set @ConferenceID = dbo.getConferenceID (@ConferenceName)
     set @ConferenceDayID = dbo.getConferenceDayID (@Date, @ConferenceID)
     set @AmountPaid = 0;
     set @Price = dbo.getConferencePrice (@ConferenceID)
     set @DueDate = dateadd(week, 1, @Date)
     set @StudentDiscount = dbo.getStudentDiscount (@ConferenceID)
     set @ConfDiscount = dbo.getConfDiscount (@ConferenceID, @Date)

     if @ConfDiscount is null
     begin
         set @ConfDiscount = 0
     end

     if dbo.isStudent(@ParticipantID) > 0
     begin
         set @AmountToPay = @Price*(1-@StudentDiscount)*(1-@ConfDiscount)
     end
     else
     begin
         set @AmountToPay = @Price*(1-@ConfDiscount)
     end
     if dbo.getConferenceDayFreeSeats (@ConferenceDayID) > 0
     begin
         insert into ConferenceDayReservation(ParticipantID, ConferenceDayID, ReservationDate, DueDate, AmountToPay, AmountPaid, Cancelled)
         values (@ParticipantID, @ConferenceDayID, GETDATE(), @DueDate, @AmountToPay, @AmountPaid, -1)
     end
 end
go


CREATE PROCEDURE Add_Participant
  @FirstName varchar(40) = null,
  @LastName varchar(40) = null,
  @Address varchar(40) = null,
  @Phone varchar(40) = null
  as
  begin
      set nocount on;
      insert into Participants(FirstName, LastName, Address, Phone)
      values (@FirstName, @LastName, @Address, @Phone)
  end
go


create procedure Add_Student
  @ParticipantID int,
  @StudentCardID int
  as
  begin
      set nocount on;
      set @ParticipantID = (
          select ParticipantID
          from Participants
          where ParticipantID = @ParticipantID
      )
      insert into Students(ParticipantID, StudentCardID)
      values (@ParticipantID, @StudentCardID)
  end
go


CREATE PROCEDURE Add_Workshop
@ConferenceName varchar(40),
@WorkshopName varchar(40),
@Date date,
@Prise int,
@ParticipantsLimit int,
@StartTime datetime,
@EndTime datetime
AS
BEGIN
  SET NOCOUNT ON
  Declare @ConferenceID as int
  set @ConferenceID = (
          select ConferenceID
          from Conferences
          where ConferenceName = @ConferenceName         -- ConfName must be unique
  )
  DECLARE @ConferenceDayID AS int
  SET @ConferenceDayID = [dbo].getConferenceDayID (@Date, @ConferenceID)
  INSERT INTO Workshops(ConferenceID, ConferenceDayID, WorkshopName, ParticipantsLimit, StartTime, EndTime, Price)
  VALUES (@ConferenceID, @ConferenceDayID, @WorkshopName, @ParticipantsLimit, @StartTime, @EndTime, @Prise)
END
go

CREATE procedure Add_Workshop_Participant
  @ConferenceName varchar(40),
  @Date date,
  @WorkshopName varchar(40),
  @ParticipantID int
  as
  begin
      set nocount on;
      declare @WorkshopID as int
      declare @ConferenceDayID as int
      declare @ConferenceID as int


      set @WorkshopID = dbo.getWorkshopID (@Date, @ConferenceName, @WorkshopName)
      set @ConferenceID = dbo.getConferenceID (@ConferenceName)
      set @ConferenceDayID = dbo.getConferenceDayID (@Date, @ConferenceID)

      if dbo.getWorkshopFreeSeats (@WorkshopID) > 0
             and dbo.isConferenceParticipant (@ConferenceDayID, @ParticipantID) > 0
             and dbo.isAlreadyParticipating (@ParticipantID, @WorkshopID) < 0
      begin
          insert into WorkshopParticipants(WorkshopID, ParticipantID, ConferenceDayID, Cancelled)
          values (@WorkshopID, @ParticipantID, @ConferenceDayID, -1)
      end
  end
go


CREATE procedure Add_Workshop_Reservation
  @ConferenceName varchar(40),
  @Date date,
  @WorkshopName varchar(40),
  @ParticipantID int
  as
  begin
      set nocount on;
      declare @WorkshopID as int
      declare @ConferenceDayID as int
      declare @DueDate as date
      declare @AmountToPay as float
      declare @AmountPaid as float
      declare @ConferenceID as int

      set @ConferenceID = dbo.getConferenceID (@ConferenceName)
      set @WorkshopID = dbo.getWorkshopID (@Date, @ConferenceName, @WorkshopName)
      set @ConferenceDayID = dbo.getConferenceDayID (@Date, @ConferenceID)
      set @DueDate = dateadd(week, 1, @Date)
      set @AmountToPay = dbo.getWorkshopPrice(@WorkshopID)
      set @AmountPaid = 0

      if dbo.getWorkshopFreeSeats (@WorkshopID) > 0
             and dbo.isConferenceParticipant (@ParticipantID, @ConferenceDayID) > 0
             and dbo.isAlreadyParticipating (@ParticipantID, @WorkshopID) < 0
      begin
          insert into WorkshopReservation(ParticipantID, WorkshopID, ConferenceDayID, ReservationDate, DueDate, AmountToPay, AmountPaid, Cancelled)
          values (@ParticipantID, @WorkshopID , @ConferenceDayID, GETDATE(), @DueDate, @AmountToPay, @AmountPaid, -1)
      end
  end
go


create procedure Cancel_Conference_Day_Participation
  @ConferenceName varchar(40),
  @ParticipantID int,
  @Date date
  as
  begin
      set nocount on;
      declare @ConferenceID as int
      declare @ConferenceDayID as int

      set @ConferenceID = dbo.getConferenceID (@ConferenceName)
      set @ConferenceDayID = dbo.getConferenceDayID (@Date, @ConferenceID)
      update ConferenceParticipants
      set Cancelled = 1
      where ConferenceDayID = @ConferenceDayID and ParticipantID = @ParticipantID
  end
go


create procedure Cancel_Conference_Day_Reservation
  @ConferenceName varchar(40),
  @ParticipantID int,
  @Date date
  as
  begin
      set nocount on;
      declare @ConferenceID as int
      declare @ConferenceDayID as int

      set @ConferenceID = dbo.getConferenceID (@ConferenceName)
      set @ConferenceDayID = dbo.getConferenceDayID (@Date, @ConferenceID)
      update ConferenceDayReservation
      set Cancelled = 1
      where ConferenceDayID = @ConferenceDayID and ParticipantID = @ParticipantID
  end
go


create procedure Cancel_Unpaid_Reservations
  as
  begin
      set nocount on;
      begin transaction
          update ConferenceDayReservation
          set Cancelled = 1
          where GETDATE() >= DueDate

          if @@error <> 0
          begin
              raiserror ('error', 16, 1)
              rollback transaction
          end

          update WorkshopReservation
          set Cancelled = 1
          where GETDATE() >= DueDate

          if @@error <> 0
          begin
              raiserror ('error', 16, 1)
              rollback transaction
          end
      Commit transaction
  end
go


create procedure Cancel_Workshop_Participation
  @ParticipantID int,
  @Date date,
  @ConferenceName varchar(40),
  @WorkshopName varchar(40)
  as
  begin
      set nocount on;
      declare @WorkshopID as int

      set @WorkshopID = dbo.getWorkshopID (@Date, @ConferenceName, @WorkshopName)
      update WorkshopParticipants
      set Cancelled = 1
      where WorkshopID = @WorkshopID and ParticipantID = @ParticipantID
  end
go


create procedure Cancel_Workshop_Reservation
  @ParticipantID int,
  @Date date,
  @ConferenceName varchar(40),
  @WorkshopName varchar(40)
  as
  begin
      set nocount on;
      declare @WorkshopID as int

      set @WorkshopID = dbo.getWorkshopID (@Date, @ConferenceName, @WorkshopName)
      update WorkshopReservation
      set Cancelled = 1
      where WorkshopID = @WorkshopID and ParticipantID = @ParticipantID
  end
go


create procedure Conference_Payment
  @ParticipantID varchar(40),
  @ConferenceName varchar(40),
  @Date date,
  @Amount money
  as
  begin
      set nocount on;
      declare @ConferenceID as int
      declare @ConferenceDayID as int

      set @ConferenceID = dbo.getConferenceID (@ConferenceName)
      set @ConferenceDayID = dbo.getConferenceDayID (@Date, @ConferenceID)
      update ConferenceDayReservation
      set AmountPaid += @Amount
      where ConferenceDayID = @ConferenceDayID and ParticipantID = @ParticipantID
  end
go

create procedure Workshop_Payment
  @ParticipantID int,
  @Amount money,
  @WorkshopName varchar(40),
  @ConferenceName varchar(40),
  @Date date
  as
  begin
      set nocount on;
      declare @WorkshopID as int

      set @WorkshopID = dbo.getWorkshopID (@Date, @ConferenceName, @WorkshopName)   -- chyba trigger wleci

      update WorkshopReservation
      set AmountPaid += @Amount
      where ParticipantID = @ParticipantID and WorkshopID = @WorkshopID and Cancelled = -1
  end
go

CREATE procedure Add_Group_Workshop_Conference_Day_Reservation
  @NumberOfSeats int,
  @ConferenceName varchar(40),
  @Date date,
  @WorkshopName varchar(40)
  as
  begin
      set nocount on;
      declare @WorkshopID as int
      declare @ParticipantID as int
      declare @ConferenceID as int
      declare @ConferenceDayID as int
      declare @CompanyName as varchar(40)

      set @ConferenceID = dbo.getConferenceID (@ConferenceName)
          set @CompanyName = (
          select CompanyName
          from Companies
          where ClientID in (
              select Conferences.ClientID
              from Conferences
              where ConferenceID = @ConferenceID
          )
      )
      set @ConferenceDayID = dbo.getConferenceDayID (@Date, @ConferenceID)

      set @WorkshopID = dbo.getWorkshopID (@Date, @ConferenceName, @WorkshopName)

      if dbo.getWorkshopFreeSeats (@WorkshopID) >= @NumberOfSeats and dbo.getConferenceDayFreeSeats (@ConferenceDayID) >= @NumberOfSeats
      begin
          while @NumberOfSeats > 0
          begin
              exec dbo.Add_Participant
              set @ParticipantID = (
                  select max(ParticipantID)
                  from Participants
              )
              exec dbo.Add_Conference_Day_Reservation @ParticipantID = @ParticipantID, @ConferenceName = @ConferenceName, @Date = @Date
              exec dbo.Add_Workshop_Reservation @ConferenceName = @ConferenceName, @Date = @Date, @WorkshopName = @WorkshopName, @ParticipantID = @ParticipantID
              exec dbo.Add_Company_Employee @CompanyName = @CompanyName, @ParticipantID = @ParticipantID
              set @NumberOfSeats -= 1
          end
      end
  end
go


CREATE procedure Add_Group_Conference_Day_Reservation
  @NumberOfSeats int,
  @ConferenceName varchar(40),
  @Date date
  as
  begin
      set nocount on;
      declare @ConferenceID as int
      declare @ConferenceDayID as int
      declare @ParticipantID as int
      declare @CompanyName as varchar(40)

      set @ConferenceID = dbo.getConferenceID (@ConferenceName)

      set @CompanyName = (
          select CompanyName
          from Companies
          where ClientID in (
              select Conferences.ClientID
              from Conferences
              where ConferenceID = @ConferenceID
          )

      )
      set @ConferenceDayID = dbo.getConferenceDayID (@Date, @ConferenceID)

      if dbo.getConferenceDayFreeSeats (@ConferenceDayID) >= @NumberOfSeats
      begin
          while @NumberOfSeats > 0
          begin
              exec dbo.Add_Participant
              set @ParticipantID = (
                  select max(ParticipantID)
                  from Participants
              )
              exec dbo.Add_Conference_Day_Reservation @ParticipantID = @ParticipantID, @ConferenceName = @ConferenceName, @Date = @Date
              exec dbo.Add_Company_Employee @CompanyName = @CompanyName, @ParticipantID = @ParticipantID
              set @NumberOfSeats -= 1
          end
      end
  end
go


CREATE TRIGGER CancelWorkshopReservation
   ON ConferenceDayReservation
   FOR UPDATE AS
   BEGIN
         IF UPDATE(Cancelled)
         BEGIN
             DECLARE @PID AS int
             set @PID = (SELECT ParticipantID FROM inserted)

             UPDATE WorkshopReservation
             SET Cancelled = 1
             WHERE @PID = WorkshopReservation.ParticipantID
         end
   end


CREATE TRIGGER CancelConferenceReservation
  ON ConferenceParticipants
  FOR UPDATE AS
  BEGIN
        IF (SELECT COUNT(*) from inserted) = 1
        BEGIN
            DECLARE @PID AS int
            set @PID = (SELECT ParticipantID FROM inserted)

            UPDATE ConferenceDayReservation
            SET Cancelled = 1
            WHERE @PID = ConferenceDayReservation.ParticipantID
        end
  end


CREATE TRIGGER CancelWorkshopReservationWhenRegistrationIsCanceled
  ON WorkshopParticipants
  FOR UPDATE AS
  BEGIN
        IF (SELECT COUNT(*) from inserted) = 1
        BEGIN
            DECLARE @PID AS int
            set @PID = (SELECT ParticipantID FROM inserted)

            UPDATE WorkshopReservation
            SET Cancelled = 1
            WHERE @PID = WorkshopReservation.ParticipantID
        end
  end


CREATE VIEW UpcomingConferences AS
SELECT Conferences.ConferenceName, Conferences.StartTime, Conferences.EndTime,
    SUM(ConferenceDays.ParticipantsLimit) AS ParticipantsLimit,
    (select count(*) from ConferenceDays
    inner join ConferenceParticipants
        on ConferenceParticipants.ConferenceDayID = ConferenceDays.ConferenceDayID
        where ConferenceDays.ConferenceID = Conferences.ConferenceID) +
     (select count (*) from ConferenceDayReservation
     inner join ConferenceDays on ConferenceDayReservation.ConferenceDayID = ConferenceDays.ConferenceDayID
     inner join Conferences C on ConferenceDays.ConferenceID = C.ConferenceID
         where C.ConferenceID = ConferenceDays.ConferenceID and
               ((GETDATE() < DueDate) or (GETDATE() > DueDate and AmountPaid > AmountToPay)))
        as 'CurrentParticipantsAndReservation'
FROM Conferences LEFT OUTER JOIN ConferenceDays ON Conferences.ConferenceID = ConferenceDays.ConferenceID
WHERE (Conferences.EndTime >= GETDATE())
GROUP BY Conferences.ConferenceID, Conferences.ConferenceName, Conferences.StartTime, Conferences.EndTime

CREATE VIEW UpcomingWorkshops AS
 select conf.ConferenceName, Workshops.WorkshopName, Workshops.Price, Workshops.ParticipantsLimit - (
     (select count (*) from WorkshopReservation as wr
     where wr.WorkshopID = Workshops.WorkshopID and
              ((GETDATE() < DueDate) or (GETDATE() > DueDate and AmountPaid > AmountToPay)))
     +
     (select count(*) from WorkshopParticipants
         where WorkshopParticipants.WorkshopID = Workshops.WorkshopID)
     ) as 'AvailablePlaces', Workshops.StartTime, Workshops.EndTime
 from Workshops
 inner join Conferences as conf on Workshops.ConferenceID = conf.ConferenceID
 where conf.EndTime >= GETDATE()


CREATE VIEW UnpaidConferenceDayReservation AS
 select c.ConferenceName, cd.Date, P.FirstName + ' ' + P.LastName as 'Name',
 cdr.AmountPaid, cdr.AmountToPay, cdr.AmountToPay - cdr.AmountPaid as 'DifferenceToPay'
 from ConferenceDayReservation as cdr
 inner join ConferenceDays CD on cdr.ConferenceDayID = CD.ConferenceDayID
 inner join Conferences C on CD.ConferenceID = C.ConferenceID
 inner join Participants P on cdr.ParticipantID = P.ParticipantID
 where cdr.AmountPaid < cdr.AmountToPay and getdate() > cdr.DueDate;


CREATE VIEW UnpaidWorkshopReservations AS
 SELECT C.ConferenceName, CD.Date, W.WorkshopName, P.FirstName + ' ' + P.LastName + ' ' + P.Address as 'Name'
from WorkshopReservation
inner join Workshops W on WorkshopReservation.WorkshopID = W.WorkshopID
inner join ConferenceDays CD on W.ConferenceDayID = CD.ConferenceDayID
inner join Conferences C on CD.ConferenceID = C.ConferenceID
inner join Participants P on WorkshopReservation.ParticipantID = P.ParticipantID
where WorkshopReservation.AmountPaid < WorkshopReservation.AmountToPay
AND GETDATE() > cd.Date;


CREATE VIEW ParticipantsOfUpcomingConferences AS
SELECT ConferenceName, CD.Date, P.FirstName + ' ' + P.LastName + ' ' + P.Address as 'Participant'
FROM Conferences
inner join ConferenceDays CD on Conferences.ConferenceID = CD.ConferenceID
inner join ConferenceParticipants CP on CD.ConferenceDayID = CP.ConferenceDayID
inner join Participants P on CP.ParticipantID = P.ParticipantID
where  CP.Cancelled <> 1 and CD.Date > GETDATE();


CREATE VIEW ParticipantsOfUpcomingWorkshops AS
SELECT C.ConferenceName, CD.Date, P.FirstName + ' ' + P.LastName + ' ' + P.Address as 'Participant'
FROM Workshops W
inner join WorkshopParticipants WP on W.WorkshopID = WP.WorkshopID
inner join Participants P on WP.ParticipantID = P.ParticipantID
inner join ConferenceDays CD on W.ConferenceDayID = CD.ConferenceDayID
inner join Conferences C on CD.ConferenceID = C.ConferenceID
where  WP.Cancelled <> 1 and CD.Date > GETDATE();


CREATE VIEW OrganizedConferencesByClients AS
 SELECT iif(I.ClientID IS NULL, Cp.CompanyName, I.FirstName + ' ' + I.LastName) as 'Client',
        count(C.ClientID) as 'NumberOfOrganizedConferences'
from Clients
left join Individuals I on Clients.ClientID = I.ClientID
left join Companies Cp on Clients.ClientID = Cp.ClientID
left join Conferences C on Clients.ClientID = C.ClientID
group by iif(I.ClientID IS NULL, Cp.CompanyName, I.FirstName + ' ' + I.LastName)


CREATE VIEW ConferenceReservedPlacesByClients AS
SELECT cp.CompanyName, C.ConferenceName, CD.Date, count(*) AS 'NumberOfReservedPlaces'
from Clients
inner join  Companies Cp on Clients.ClientID = Cp.ClientID
inner join Conferences C on Clients.ClientID = C.ClientID
inner join ConferenceDays CD on C.ConferenceID = CD.ConferenceID
inner join ConferenceDayReservation CDR on CD.ConferenceDayID = CDR.ConferenceDayID
inner join ConferenceParticipants P on CD.ConferenceDayID = P.ConferenceDayID
inner join Participants P2 on CDR.ParticipantID = P2.ParticipantID
where P2.FirstName IS NULL and P2.LastName IS NULL and P2.Address is null and P2.Phone IS NULL
and datediff(day, GETDATE(), C.StartTime) > 14
group by cp.CompanyName, C.ConferenceName, CD.date



CREATE VIEW WorkshopReservedPlacesByClients AS
SELECT cp.CompanyName, C.ConferenceName, W.WorkshopName, count(*) AS 'NumberOfReservedPlaces'
from Clients
inner join Companies Cp on Clients.ClientID = Cp.ClientID
inner join Conferences C on Clients.ClientID = C.ClientID
inner join Workshops W on C.ConferenceID = W.ConferenceID
left join WorkshopReservation WR on W.WorkshopID = WR.WorkshopID
left join Participants P2 on WR.ParticipantID = P2.ParticipantID
where P2.FirstName IS NULL and P2.LastName IS NULL and P2.Address is null and P2.Phone IS NULL
and datediff(day, GETDATE(), C.StartTime) > 14
group by cp.CompanyName, C.ConferenceName, W.WorkshopName;



create view ResignationsByParticipants AS
select iif(Participants.FirstName IS NULL,
 C3.CompanyName,
 Participants.FirstName + ' ' + Participants.LastName + ' ' + Participants.Address)  as 'Participant' ,
    count(cdr.ParticipantID) + count(cp.ParticipantID) AS 'AmountOfResignationOfConferences',
    count(wp.ParticipantID) + count(wr.ParticipantID) AS 'AmountOfResignationOfWorkshops'
from Participants
left join ConferenceDayReservation CDR on Participants.ParticipantID = CDR.ParticipantID
left join ConferenceParticipants CP on Participants.ParticipantID = CP.ParticipantID
left join WorkshopParticipants WP on CP.ParticipantID = WP.ParticipantID and CP.ConferenceDayID = WP.ConferenceDayID
left join WorkshopReservation WR on CDR.ParticipantID = WR.ParticipantID and CDR.ConferenceDayID = WR.ConferenceDayID
left join ConferenceDays CD on CDR.ConferenceDayID = CD.ConferenceDayID
left join Conferences C on CD.ConferenceID = C.ConferenceID
left join Clients C2 on C.ClientID = C2.ClientID
left join Companies C3 on C2.ClientID = C3.ClientID
where cdr.Cancelled = 1 or cp.Cancelled = 1 or wp.Cancelled = 1 or wr.Cancelled = 1
group by  C3.CompanyName, Participants.FirstName, Participants.LastName, Participants.Address;
